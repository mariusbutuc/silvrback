Be careful the environment you choose
for it will shape you

[Michael Hartl](https://github.com/mhartl) mentions in his Rails Tutorial that Rails comes equipped with three environments: `test`, `development`, and `production`.
Then [an aside](https://www.railstutorial.org/book/sign_up#aside-rails_environments) shows how to run a console, a server and a rake task in an environment different from the implicit `development` because, as he confesses

> I find it confusing that the console, server, and migrate commands specify non-default environments in three mutually incompatible ways, which is why I bothered showing all three.

Amongst the minds that cope better with consistent patterns, I also count mine. The trick that seems to save me some brain cycles is easy, _almost too obvious_. Declare the `RAILS_ENV` environment variable right before the command, on the same line. In few cases this becomes slightly less elegant, but at least it's consistent.

For the sake of explicitness, here are the alternatives:
_(note: the examples use the [`spring`](https://github.com/rails/spring)ified executables)_

| the _mutually incompatible_ way       | the _consistent pattern_ equivalent |
| ------------------------------------- | ----------------------------------- |
| `bin/rails console test`              | `RAILS_ENV=test rails console`      |
| `bin/rails server --environment test` | `RAILS_ENV=test rails server`       |
| `bin/rake db:migrate RAILS_ENV=test`  | `RAILS_ENV=test rake db:migrate`    |

_Almost too obvious_, you had been warned!

What other quirks do you leverage to save yourself extra brain cycles?
