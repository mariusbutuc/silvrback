# Elixir Bootcamp
## Notes on the Elixir and Phoenix Bootcamp, part 1

These notes come as breadcrumbs left along [my journey][my-journey] through [the Complete Elixir and Phoenix Bootcamp][on-udemy]. This is the first part that covers discovering Elixir. The second part will cover Phoenix. Here we go:

  1. [There are no methods in Elixir—just **functions**][elixir-functions]:

    > There are no objects in Elixir (nor methods). If they are about structs, then they should be referred to as structs (or generally as data) (and use "functions" instead of "methods").

    –José Valim

  1. [List comprehension][list-comprehension]
    + create a list based on existing lists
    + _distinct_ from the use of map and filter functions

  1. We played with [Lists][docs-list], let's learn about [Tuples][docs-tuple]:
    +  "like an array, where each index has a very special meaning"
    + the ordering / contract is in the developer's head!
    + can be seen as a key/value pair, where the key is the index

  1. Pattern Matching
    + "Elixir's replacement for variable assignment"
    + Elixir, as a language governed by its design
    + `=` starts the pattern matching sequence
      * create a mirror structure on the left hand side
        - matches the data structure
        - matches the number of values / elements
    + enables you to _avoid_ writing `if` statements
      * pattern matching in `case` statements
    + pattern matching can also be done directly in the argument list

  1. The Elixir Ecosystem
    + **Code** we write » 
      * _fed into_ » **Elixir** » 
      * _transpiled into_ » **Erlang** » 
      * _compiled and executed on_ » **BEAM**
    + leveraging underlying Erlang modules:
      * `:erlang.term_to_binary/1`
      * `:erlang.binary_to_term/1`
      * `:file.format_error/1` —[thanks @pragdave][programming-elixir]
      * `:egd` —the [erlang graphical drawer][erlang-egd]

  1. Atoms
    + personally, I prefer the definition from [Programming Elixir 1.3][programming-elixir]

      > Atoms are constants that represent something’s name. […]  
      > An atom’s name is its value. Two atoms with the same name will always compare as being equal, even if they were created by different applications on two computers separated by an ocean.

  1. Unused variables
    + Elixir provides a friendly warning at compile time
    + Solution: underscore them: `_` or `_reason`

  1. The Pipe operator
    + the key is to write functions that take consistent first arguments
      * the return of the previous function gets applied as the first argument in the subsequent function
    + [Joe Armstrong's explanation of the pipe operator][a-week-with-elixir] made the most sense for me:  

      > This is the recessive monadic gene of Prolog. The gene was dominant in Prolog, recessive in Erlang (son-of-prolog) but re-emerged in Elixir (son-of-son-of-prolog).  
      > 
      > `x |> y*` means call `x` then take the output of `x` and add it as an extra argument to `y` in the first argument position.  
      > 
      > So
      > 
      > ```elixir
      > x(1,2) |> y(a,b,c) 
      > ```
      > 
      > Means
      > 
      > ```elixir
      > newvar = x(1,2); 
      > y(newvar,a,b,c);
      > ```

  1. Documentation
    + [ExDoc][ex_doc]
      * `@moduledoc` for module documentation
      * `@doc` for function documentation
      * `mix docs` to generate

  1. Testing
    + first-class citizen: fully featured out of the box
    + `mix test`
    1. [Doctests][getting-started-doctests]
      * write docs and tests at the same time
      * examples in documentation stay up to date
      * Scenario: explain to another engineer the simplest case of using a given function
      * Additional assertions are _possible_, but you generally want to make only one assertion about the very last line: this is a documentation test about the `contains?/2` function, and not about `create_deck/0`. So in practice we want to have just one, very small, very targeted assertion about the given function.
      * Ran by the `doctest Cards` line inside out test file
        - parse the module
        - run any examples as an actual test
    1. Case Tests
      * What behaviour do we want to test?
      * `assert` versus `refute`
      * Elixir's functional programming style makes it so easy to test
        - create basic object, representing our working data
        - pass it off to the function we're testing
        - do some basic checks on the returned object

  1. Maps
    + key-value stores, similar to Ruby hashes, or Javascript objects  

          ```elixir
          colours = %{primary: "red", secondary: "blue"}
          ```
    + accessing properties
      * dot notation
      * pattern matching
    + updating values
      * immutable data: to update means to create a new data structure with the modifications
      * two ways of achieving this:
        - with a function: [`Map.put/3`][docs-map], [etc][map-update-syntax]
        - leveraging the [built in syntax][getting-started-structs] —[similar to Elm][elm-update-records]

            ```elixir
            %{colours | primary: "green"}
            ```

          + only works for existing keys:

            > the VM is aware that **no new keys will be added** to the struct, allowing the maps underneath to share their structure in memory.

  1. Keyword Lists
    + `List` and `Tuple` merged into one
      * lists: like arrays, can be used for an arbitrary number of elements
      * tuples: like arrays, where each index has a special meaning to us
    + versus `Map`: there can be duplicate keys
    + a list that contains tuples

        ```elixir
        colours = [{:primary, "red"}, {:secondary, "blue"}]
        # or
        colours = [primary: "red", secondary: "blue"]
        ```

  1. Identicon Image Manipulation
    + describing the business logic
      * one root object / piece of data
      * gets passed around through a Main Pipeline
        - a series of small functions, that transform the data

  1. Data Modeling
    + `Struct`
      * it's just a `Map` plus
        - compile-time checks
        - default values
      * Looks a lot like a `Map`; why would we use a `Struct` over a `Map`?
        - a `Struct` enforces that the only properties that can be stored are the ones defined in the module
        - a normal `Map` is happy to let you insert any different property that you'd like
      * Why not add functions (methods / properties / instance methods) to structs? From an FP perspective
        - it's a Map under the hood
        - has no ability to attach any functions to it
        - it can only hold some primitive data
      * A single location to store all the data inside of our app
      * The [update syntax][getting-started-structs] works best, as properties on a `Struct` are already defined
    + `List`
      * Acessing the first X values from an arbitrarily long list
        - the head and tail split pattern `[h | t]`
        - extended to `[a, b, c | _tail]`
    + `Tuple`
      * use a `Tuple` instead of a `List` when the index has particular semantic

  1. First-class functions
    + in Elixir, referring to a function—`mirror_row`—it will call it by default
    + to pass a reference to a function, we use [a special syntax][course-first-class-functions]
    + Elixir provides guidance even through its compile error

      > […] invalid args for `&`, expected an expression in the format of `&Mod.fun/arity`, `&local/arity` or a capture containing at least one argument as `&1`

  1. [Anonymous Functions][closure-vs-named-function]
    + we can't mix clauses that expect a different number of arguments. A  function always has a fixed arity.
    + anonymous functions are closures, similar to lambdas in Ruby

  1. A working Identicon program
    + the pipe operator
    + working with Erlang ([`egd`][erlang-egd])
    + `Enum`
    + anonymous functions

The second part will cover Phoenix. Back to the bootcamp now!

  [a-week-with-elixir]: http://joearms.github.io/2013/05/31/a-week-with-elixir.html#head_7
  [closure-vs-named-function]: http://stackoverflow.com/a/18023790/341929
  [course-first-class-functions]: http://elixir-lang.org/crash-course.html#first-class-functions
  [docs-list]: http://elixir-lang.org/docs/stable/elixir/List.html
  [docs-map]: http://elixir-lang.org/docs/stable/elixir/Map.html
  [docs-tuple]: http://elixir-lang.org/docs/stable/elixir/Tuple.html
  [elixir-functions]: https://elixirforum.com/t/there-are-no-methods-in-elixir-just-functions/2451
  [elm-update-records]: http://elm-lang.org/docs/records#updating-records
  [erlang-egd]: http://erlang.org/doc/man/egd.html
  [ex_doc]: https://github.com/elixir-lang/ex_doc
  [getting-started-doctests]: http://elixir-lang.org/getting-started/mix-otp/docs-tests-and-with.html#doctests
  [getting-started-structs]: http://elixir-lang.org/getting-started/structs.html#accessing-and-updating-structs
  [list-comprehension]: https://en.wikipedia.org/wiki/List_comprehension
  [map-update-syntax]: https://dockyard.com/blog/2016/03/07/til-elixir-maps-have-built-in-syntax-for-updating
  [my-journey]: https://github.com/mariusbutuc/my-ude/tree/master/complete_elixir_phoenix_bootcamp
  [on-udemy]: https://www.udemy.com/the-complete-elixir-and-phoenix-bootcamp-and-tutorial
  [programming-elixir]: https://pragprog.com/book/elixir13/programming-elixir-1-3
