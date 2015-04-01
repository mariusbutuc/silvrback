# Testing async emails, the Rails 4.2+ way

## The scenario is simple

It's time for our application to send some emails. And yes, we agree to never block the controller, so async delivery is the way to go.

The good news is that since Rails 4.2, [sending emails asynchronously][rails-42-async-mail] is easier than ever before. In our app, Sidekiq was chosen as the queuing system, but since `ActionMailer#deliver_later` is built on top of `ActiveJob`, the interface is clean and queuing system agnostic. This means that if I wouldn't have just mentioned it, you couldn't tell—this works the same way for Resque, Delayed Job or anything else.

## On the shoulders of Active Job

If you're new to Rails 4.2—or to [Active Job][rails-42-active-job] in general—[Ben Lewis' intro to Active Job][start-aj] is a great place to start. One detail it leaves me [wishing for][tweet-a-wish] though is a clean, idiomatic approach to testing that everything works as expected.
So for the purpose of this article, we'll assume you have

  * Rails 4.2 or greater;
  * Active Job set up to use a queueing backend (Sidekiq, Resque, etc);
  * a Mailer.

To keep things simple and focus on what's important, say we want to send the user a welcome email once they join; just like in [the Rails guides mailer example][calling-the-mailer]:

```ruby
# app/controllers/users_controller.rb

class UsersController < ApplicationController
  …
  def create
    …
    # Yes, I prefer the Ruby 2.0+ keyword arguments
    UserMailer.welcome_email(user: @user).deliver_later
  end
end
```

## The mailer should do its job… eventually

Next we want to ensure the job inside the controller does what we expect. In the testing guides, the section on [custom assertions for testing jobs inside other components][aj-custom-assert] teaches us about half a dozen of such custom assertions.

I wouldn't blame you if your first instinct is to dive right in and use [`assert_enqueued_jobs`][assert-enqueued-jobs] to test if we're enqueueing a mail delivery job every time we're creating a new user:

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

only to be surprised by the failing test screaming that `assert_enqueued_jobs` is not defined for us to use.

This is because our test inherits from `ActionController::TestCase` which, at the time of writing, does not include `ActiveJob::TestHelper`. But we can quickly fix this:

```ruby
# test/test_helper.rb

class ActionController::TestCase
  include ActiveJob::TestHelper
  …
end
…
```

Granted that our code does what we expect, our test should now be green. This is good news: we can either move to refactoring our code, adding new functionality or new tests. Let's go with the latter and test if our email is delivered and if it has the expected content.

When delivering emails inline, it was as easy as asserting that we had an extra delivery:

```ruby
assert_difference 'ActionMailer::Base.deliveries.size', +1 do
  post :create, {…}
end
```

In our async world, we need first to [`perform_enqueued_jobs`][perform-enqueued-jobs]

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

## Conclusion

Having Action Mailer standing on the shoulders of Active Job, makes switching from sending emails right away to sending them via the queue as easy as switching `deliver_now` to `deliver_later`. Since Active Job makes setting up your job infrastructure—without knowing too much about what queueing system you're using—so much easier, your tests shouldn't care either if you're going to switch to Sidekiq or Resque in the future.

It can be a little bit tricky to get your tests set up correctly, so they can take full advantage of the new custom assertions provided by Active Job. Hopefully, this tutorial made the process a little more transparent for you.

Please feel free to share your thoughts via twitter and, until next time, happy hacking!


  [start-aj]: https://blog.engineyard.com/2014/getting-started-with-active-job "Getting Started With Active Job"
  [tweet-a-wish]: https://twitter.com/mariusbutuc/status/578585047059599361 "Wish it would have touched testing too"
  [rails-42-async-mail]: http://guides.rubyonrails.org/4_2_release_notes.html#asynchronous-mails "Rails 4.2 Release Notes: Asynchronous Mails"
  [rails-42-active-job]: http://guides.rubyonrails.org/4_2_release_notes.html#active-job "Rails 4.2 Release Notes: Active Job"
  [calling-the-mailer]: http://guides.rubyonrails.org/action_mailer_basics.html#calling-the-mailer "Action Mailer Basics: Calling the Mailer"
  [aj-custom-assert]: http://guides.rubyonrails.org/testing.html#custom-assertions-and-testing-jobs-inside-other-components "Custom Assertions And Testing Jobs Inside Other Components"
  [assert-enqueued-jobs]: http://api.rubyonrails.org/classes/ActiveJob/TestHelper.html#method-i-assert_enqueued_jobs
  [perform-enqueued-jobs]: http://api.rubyonrails.org/classes/ActiveJob/TestHelper.html#method-i-perform_enqueued_jobs
