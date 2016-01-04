# Code coverage analysis tools
SimpleCov: how to add it to your tool belt and (the 5) why(s)

## Why am I reading this?

Popular software development culture advises us that 100% test coverage of our code is no reason for pride. It's not even a goal to be pursued on high priority. Either taught by our experience already, or we've overheard at least an _elder_ (developer) say it before: having test that provide complete code coverage does necessarily mean that our code is good, less so that it is correct.
Tests written after more tests don't always grant us confidence.

Agreed! But having blind spots or not even being aware of the existing blind spots can surely diminish our level of confidence that PagerDuty will not wake us up at 3 in the morning…

If you're a veteran in the field, and you've already reached this paragraph, maybe it's time to hop back on HN for something more worth your time. This one is for the novice, for the apprentice, that five minutes ago would have felt intimidated to hear about a _"code&nbsp;coverage analysis tool"_.

## Why bring it up now?

The [RailsTutorial.org](https://www.railstutorial.org/book) is one of the resources I sometimes mention when asked about a handy, structured way to get up to speed with Rails, with some Ruby, even with basic idioms of developing for the web. It's a nice, gradual intro aimed to best serve the novice. Oftentimes, Michael Hartl leverages footnotes and asides to open doors and inspire the reader to explore further. Some other times though, it's easy to still find opportunities for improvement.
And speaking of testing, [at one point](https://www.railstutorial.org/book/log_in_log_out#sec-testing_the_remember_branch) the reasoning goes like this:

> […] we verified by hand that the persistent session implemented in the preceding sections is working, but in fact the relevant branch in the `current_user` method is currently completely untested. My favorite way to handle this kind of situation is to raise an exception in the suspected untested block of code: if the code isn't covered, the tests will still pass; if it is covered, the resulting error will identify the relevant test.

and here the author gives an example:

[![Code-branch raise](https://silvrback.s3.amazonaws.com/uploads/d5c51d53-1737-4404-8a47-3b9f9571125d/railstutorial-uncovered-paths_large.png)](https://www.railstutorial.org/book/log_in_log_out#_code-branch_raise)

## Why is it better?

Keeping _a beginner&nbsp;mindset_, one is expected to understand all the complete paths that their tests are currently covering. Only then can we start to speculate on the quick wins of raising an exception. Hey, time for a quick time travel: how many of us can be proud about the design and the simplicity of our first Rails app? Was it really that simple to fully grasp it on the first take?

This is where I believe a code coverage analysis tool shines: in its simplicity and ease of use, relative to the amount of insight provided. We all strive to increase the number of _feedback&nbsp;loops_ that we can leverage. And the quicker, shorter those loops are, the better. So when it comes to _static code analysis_ and _code&nbsp;metrics_ for Ruby, [SimpleCov](https://github.com/colszowka/simplecov) is probably one of the most used tools, next to [RuboCop](https://github.com/bbatsov/rubocop) and others.

## Why… wait; how do I use SimpleCov?

OK, let's open up our Rails project in your favourite text editor.

1. First step, as with any gem, is to add it to our `Gemfile` and to `bundle install`.
  In the case of SimpleCov, scoping it into the `:test` group is sufficient:

  ```ruby
    # Gemfile
    group :test do
      # …
      gem 'simplecov', require: false
    end
  ```

2. Next, we need to _load_ SimpleCov at the very top of our `test/test_helper.rb`
  _(or `spec_helper.rb`, cucumber `env.rb` etc)_. I prefer the coverage analysis to be ran on demand. Hence the `SIMPLECOV` environment variable in the following example:

  ```ruby
    # test/test_helper.rb
    require 'simplecov' if ENV['SIMPLECOV']

    ENV['RAILS_ENV'] ||= 'test'
    # …
  ```

3. Time to _launch_ SimpleCov.
  The [official docs](https://github.com/colszowka/simplecov#getting-started) suggest to do it within the `test/test_helper.rb` as well, but why not keep it clean if we have the option? Even if we are not using SimpleCov to merge multiple test suite results (i.e. MiniTest and Cucumber), we can still leverage the `.simplecov` centralized config file, that we create in the root of the project.

  ```ruby
    # .simplecov
    SimpleCov.start('rails') do
      coverage_dir('public/coverage')
    end
  ```

  For the sake of the example, let's keep the configuration minimal:
    - since we are talking about a Rails application, we take advantage of the `rails` configuration that SimpleCov comes built-in with
    - the only customization is to set `public/coverage` as the output and cache directory. Since it doesn't make much sense to keep the generated files and cache under source control, let's also add this directory to our `.gitignore`:

      ```ruby
        # .gitignore
        # …
        public/coverage
      ```

4. All that's left now is to _run our tests_, specifying when we'd like our code coverage analyzed.

  ```bash
    $ SIMPLECOV=true bin/rake test
  ```

  Given our configuration from the previous step, the coverage reports can be accessed as static assets from our server, i.e. http://localhost:3000/coverage/index.html

## Why did we do this again?

To make it easy for ourselves to undestand what we've missed so far. To be _aware_ of our possible blind spots.

For a fairly trivial MVP, the big picture might looks something like this:

![SimpleCov OEJ MC](https://silvrback.s3.amazonaws.com/uploads/8414e3ce-c465-4b60-aca3-dcaeb841f66f/2016-01-04%20simplecov-oejmc_large.png)

And from the overview, we can dig deeper for details. For instance, in the earlier RailsTutorial example, if instead of relying on raising an exception we would have put SimpleCov to good use, this is the quick feedback loop available to us:

![RailsTutorial.org example](https://silvrback.s3.amazonaws.com/uploads/faa79203-68b7-46bc-a1aa-21b616193076/2016-01-02%20simplecov_large.png)

Quite the detailed feedback, ready to be acted upon, eh? :)

***

Now, this is just one tool, and for just one purpose.

What are your favourite gems or services that you use to collect code metrics?

  [cover-photo]: https://500px.com/photo/75749775/still-life-by-sergey-bogomyako
