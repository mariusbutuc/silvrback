# Testing async emails, the Rails 4.2+ way

Say you're building an app that needs to do send emails. And yes, we agree to never block the controller, so async delivery is the way to go.

The good news is that since Rails 4.2, [sending emails asynchronously][rails-42-async-mail] is easier than ever before. In our app, [Sidekiq][sidekiq] was chosen as the queuing system, but since `ActionMailer#deliver_later` is built on top of `ActiveJob`, the interface is clean and agnostic of the asynchronous processing library used. This means that if I wouldn't have just mentioned it you couldn't tell, either form a developer’s perspective, or when it comes to user experience. Setting up a queuing system is a topic on it's own, and beyond the scope of this article, so…

### Don't Sweat the Small Stuff

In our example, we assume that Sidekiq and its dependencies are properly configured, so the only piece of code that is specific to this scenario is declaring which queue adapter should Active Job use:

```ruby
# config/application.rb

module OurApp
  class Application < Rails::Application
    …
    config.active_job.queue_adapter = :sidekiq
  end
end
```

Active Job does a great job at hiding away all the nitty gritty queue implementation details, such that this works the same way for Resque, Delayed Job or anything else. So if we were to use [Sucker Punch][sucker-punch] instead, the only change would be to switch the queue adapter from `:sidekiq` to `:sucker_punch`, after meeting the gem dependency.

## On the Shoulders of Active Job

If you're new to Rails 4.2, or to [Active Job][rails-42-active-job] in general, [Ben Lewis' intro to Active Job][start-aj] is a great place to start. One detail it leaves me [wishing for][tweet-a-wish], though, is a clean, idiomatic approach to testing that everything works as expected.

So for the purpose of this article, we'll assume you have:

  * Rails 4.2 or greater
  * Active Job set up to use a queueing backend (e.g. Sidekiq, Resque, etc.)
  * A Mailer

Any Mailer should work with the concepts described here, but we'll use this welcome email to make keep our examples pragmatic:

```ruby
#app/mailers/user_mailer.rb

class UserMailer < ActionMailer::Base
  default from: 'email@example.com'

  def welcome_email(user:)
    mail(
      to: user.email,
      subject: "Hi #{user.first_name}, and welcome!"
    )
  end
end
```

To keep things simple and focus on what's important, we want to send the user a welcome email once they join.

This is just like in [the Rails guides mailer example][calling-the-mailer]:

```ruby
# app/controllers/users_controller.rb

class UsersController < ApplicationController
  …
  def create
    …
    # Yes, Ruby 2.0+ keyword arguments are preferred
    UserMailer.welcome_email(user: @user).deliver_later
  end
end
```

## The Mailer Should Do Its Job, Eventually

Next we want to ensure the job inside the controller does what we expect.

In the testing guides, the section on [custom assertions for testing jobs inside other components][aj-custom-assert] teaches us about half a dozen of such custom assertions.

Perhaps your first instinct is to dive right in and use [`assert_enqueued_jobs`][assert-enqueued-jobs] to test if we're enqueueing a mail delivery job every time we're creating a new user.

Here’s how you’d do that:

```ruby
# test/controllers/users_controller_test.rb

require 'test_helper'

class UsersControllerTest < ActionController::TestCase
  …
  test 'email is enqueued to be delivered later' do
    assert_enqueued_jobs 1 do
      post :create, {…}
    end
  end
end
```

If you do this though, you’ll surprised by the failing test that tells you `assert_enqueued_jobs` is not defined for us to use.

This is because our test inherits from `ActionController::TestCase` which, at the time of writing, does not include `ActiveJob::TestHelper`.

But we can quickly fix this:

```ruby
# test/test_helper.rb

class ActionController::TestCase
  include ActiveJob::TestHelper
  …
end
…
```

Assuming our code does what we expect, our test should now be green.

This is good news. We can either move to refactoring our code, adding new functionality, or adding new tests. Let's go with the latter and test if our email is delivered and if it has the expected content.

`ActionMailer` provides us with an array of all the emails sent out with the `delivery_method` option configured to `:test`. We can access it through `ActionMailer::Base.deliveries`. When delivering emails inline, testing that our action was successful and the email actually gets delivered is very easy. All we need to do is to check that our deliveries count was incremented by one upon completing our action. Translating this to MiniTest, it would look like this:

```ruby
assert_difference 'ActionMailer::Base.deliveries.size', +1 do
  post :create, {…}
end
```

Our tests are happening in real time, but as we agreed in the very beginning of this article to never block the controller and send emails in a background job, we now need to orchestrate everything to ensure our system is deterministic. For this reason, in our async world we need first to execute all enqueued job before we can evaluate their results. To execute the pending ActiveJob jobs we will use [`perform_enqueued_jobs`][perform-enqueued-jobs]:

```ruby
test 'email is delivered with expected content' do
  perform_enqueued_jobs do
    post :create, {…}
    delivered_email = ActionMailer::Base.deliveries.last

    # assert our email has the expected content, e.g.
    assert_includes delivered_email.to, @user.email
  end
end
```

### Shorten the Feedback Loop

We've touched functional testing so far, ensuring our controller is behaving as expected. But what about unit testing our mailers to shorten the feedback loop and get quick insights when the changes in our code could break the emails that we send out?

[The Rails guide on testing][testing-rails-mailers] suggests using fixtures here, but I find them to be too brittle. Especially in the beginning, when still experimenting with the design or the content of the email, a change can quickly render them outdated and make our tests red. Instead, my preference is to use `assert_match` to focus on key elements that should be part of the email's body.

For this purpose and more (like abstracting away the logic of handling emails that are multipart or not) we can build our custom assertions. This enables us to extend the [standard MiniTest assertions][minitest-assertions] or [the Rails specific assertions][rails-assertions]. It is also a good example of creating our own Domain Specific Language (DSL) for testing.

Let's create a `shared` folder within the `test` one to host our `SharedMailerTests` module. Our custom assert can look something like this:

```ruby
# /test/shared/shared_mailer_tests.rb

module SharedMailerTests
  …
  def assert_email_body_matches(matcher:, email:)
    if email.multipart?
      %w(text html).each do |part|
        assert_match matcher, email.send("#{part}_part").body.to_s
      end
    else
      assert_match matcher, email.body.to_s
    end
  end
end
```

Next, we need to make our mailer tests aware about this custom assertion, so let's mix it in `ActionMailer::TestCase`. We can do this in a similar fashion to the way we included `ActiveJob::TestHelper` in `ActionController::TestCase` earlier:

```ruby
# test/test_helper.rb

require 'shared/shared_mailer_tests'
…
class ActionMailer::TestCase
  include SharedMailerTests
  …
end
```

Note that we first need to require our `shared_mailer_tests` in the `test_helper`.

With this in place, we can now ensure that our emails contain the key elements that we expect. Imagine we want to make sure the URL we send the user contains some specific UTM parameters for tracking. We can now use our custom assertion in conjunction with our old friend `perform_enqueued_jobs` like so:

```ruby
# test/mailers/user_mailer_test.rb

class ToolMailerTest < ActionMailer::TestCase
  …
  test 'emailed URL contains expected UTM params' do
    UserMailer.welcome_email(user: @user).deliver_later

    perform_enqueued_jobs do
      refute ActionMailer::Base.deliveries.empty?

      delivered_email = ActionMailer::Base.deliveries.last
      %W(
        utm_campaign=#{@campaign}
        utm_content=#{@content}
        utm_medium=email
        utm_source=mandrill
      ).each do |utm_param|
        assert_email_body_matches utm_param, delivered_email
      end
    end
  end
```

## Conclusion

Having ActionMailer standing on the shoulders of Active Job makes switching from sending emails right away to sending them via the queue as easy as switching `deliver_now` to `deliver_later`.

Since Active Job makes setting up your job infrastructure (without knowing too much about what queueing system you're using) so much easier, your tests shouldn't care if you switch to Sidekiq or Resque in the future.

It can be a little bit tricky to get your tests set up correctly so that they can take full advantage of the new custom assertions provided by Active Job. Hopefully, this tutorial made the process a little more transparent for you.

P.S. Have you had experience with ActionMailer or Active Job? Any tips? Any gotchas? Let us know by leaving a comment.

  [start-aj]: https://blog.engineyard.com/2014/getting-started-with-active-job "Getting Started With Active Job"
  [tweet-a-wish]: https://twitter.com/mariusbutuc/status/578585047059599361 "Wish it would have touched testing too"
  [rails-42-async-mail]: http://guides.rubyonrails.org/4_2_release_notes.html#asynchronous-mails "Rails 4.2 Release Notes: Asynchronous Mails"
  [rails-42-active-job]: http://guides.rubyonrails.org/4_2_release_notes.html#active-job "Rails 4.2 Release Notes: Active Job"
  [calling-the-mailer]: http://guides.rubyonrails.org/action_mailer_basics.html#calling-the-mailer "Action Mailer Basics: Calling the Mailer"
  [aj-custom-assert]: http://guides.rubyonrails.org/testing.html#custom-assertions-and-testing-jobs-inside-other-components "Custom Assertions And Testing Jobs Inside Other Components"
  [assert-enqueued-jobs]: http://api.rubyonrails.org/classes/ActiveJob/TestHelper.html#method-i-assert_enqueued_jobs
  [perform-enqueued-jobs]: http://api.rubyonrails.org/classes/ActiveJob/TestHelper.html#method-i-perform_enqueued_jobs
  [sidekiq]: https://github.com/mperham/sidekiq "Sidekiq: Simple, efficient background processing for Ruby"
  [testing-rails-mailers]: http://guides.rubyonrails.org/testing.html#testing-your-mailers "Rails Guides: Testing Your Mailers"
  [minitest-assertions]: http://guides.rubyonrails.org/testing.html#available-assertions "Rails Guides: Available Assertions"
  [rails-assertions]: http://guides.rubyonrails.org/testing.html#rails-specific-assertions "Rails Guides: Rails Specific Assertions"
  [sucker-punch]: https://github.com/brandonhilkert/sucker_punch "Sucker Punch async processing library"
