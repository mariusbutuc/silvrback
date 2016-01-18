# Testing User Authentication, from the fixture level

## Our hypothetical Rails app: the _Foo Club_

Let's imagine that _Foo Club_ wants you to build an application to display events. These events are of two types&mdash;public and private: 

  - _Public_ events are visible to anyone visiting our application.  
    Say someone prospecting, to determine if the Club is a right fit.
  - _Private_ events are only visible to registered, authenticated members. 

Also, only the _admins_ should be able to edit, delete etc. events.

## Authentication: minimal, yet powerful

Our app uses a standard email and password combination to authenticate the Club's members. It does this by leveraging [`has_secure_password`](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html#method-i-has_secure_password), which adds methods to set and authenticate against a `BCrypt` password. This way, no plain text passwords are persisted in the database, but a [`password_digest`](https://github.com/rails/rails/blob/4-2-stable/activemodel/lib/active_model/secure_password.rb#L125) is what gets stored instead.

## Fixtures are but YAML files, just better

As any fan of [fixtures](http://api.rubyonrails.org/classes/ActiveRecord/FixtureSet.html), we avoid spreading definitions of different types of members all across our tests. We isolate them in one place instead, in YAML files.

Say we want to model two different kinds of users, and we pick Jax as a member of the _Foo Club_ and Tig, who's not convinced yet; he's still looking around:

```yaml
# test/fixtures/users.yml

member:
  name: Jackson Teller
  email: jax@example.com

visitor:
  name: Alexander Trager
  email: tig@example.com
```

As a member, Jax needs a password, but as we mentioned earlier, these are never stored in plain text. Instead, we need to generate the `password_digest` when the record is created, based on the unencrypted password. For a good example, let's see [how Rails does it](https://github.com/rails/rails/blob/4-2-stable/activemodel/lib/active_model/secure_password.rb#L125):

```ruby
# …
self.password_digest = BCrypt::Password.create(unencrypted_password, cost: cost)
# …
```

One advantage of fixture files over classical YAML files is that we can inject dynamic values using ERB. We can now easily inherit the logic we learnt from Rails in our fixture. Maybe the only unknown left is that `cost`: what values can it take? 

When we're running our tests, and don't care that much about encryption strength; we want them to run fast. So how low can dial this down? Looking at `bcrypt`, [the minimum cost](https://github.com/codahale/bcrypt-ruby/blob/0612960/lib/bcrypt/engine.rb#L7) supported by the algorithm is 4. Then easy:

```yaml
# test/fixtures/users.yml

member:
  name: Jackson Teller
  email: jax@example.com
  password_digest: <%= BCrypt::Password.create('password', cost: 4) %>
# …
```

## Keep it short and simple

Getting to know the big picture a little better will allow us to reduce our code one step further. [This test](https://github.com/rails/rails/blob/4-2-stable/activemodel/test/cases/railtie_test.rb#L26-L31) confirms that in the test environment [the minimum cost is used](https://github.com/rails/rails/blob/4-2-stable/activemodel/lib/active_model/secure_password.rb#L124) when computing the secure password. Armed with this knowledge, we can make our fixture file more succinct:

```yaml
# test/fixtures/users.yml

member:
  name: Jackson Teller
  email: jax@example.com
  password_digest: <%= BCrypt::Password.create('password') %>
# …
```

## Next steps

Now it's easy to go ahead writing our controller or integration tests, adding a helper method that automatically logs the member in, add different kinds of users (maybe an admin?) and check if they can delete an event while regular members, or simple visitors cannot. You name it!
