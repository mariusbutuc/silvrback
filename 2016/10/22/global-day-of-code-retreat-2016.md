Last Saturday, the Global Day of Code Retreat—Toronto Edition happened; [#gdcr16][gdcr16-toronto].

# Intro

  - today is about practicing your craft, design
  - today is not about delivering
  - Conway's Game of Life
    - base rules
      1. Any live cell with fewer than two live neighbours dies, as if caused by underpopulation.
      2. Any live cell with more than three live neighbours dies, as if by overcrowding.
      3. Any live cell with two or three live neighbours lives on to the next generation.
      4. Any dead cell with exactly three live neighbours becomes a live cell.
    - different constraints/rules are/can be added with each iteration
    - first session: no constraints, so you can get the design out of your mind
    - Kent Beck's rules of simple design
      1. Passes all tests
      2. No Duplication ("Don't Repeat Yourself")—don't forget about production vs tests duplication
      3. Reveals intent ("Good Names")
      4. Minimizes the number of classes/methods ("Small")

# First iteration

  - language: JavaScript
  - how do we quickly get a TDD-ready environment set up? [cyber‑dojo.org][cyber-dojo]?
  - only two states: alive or dead

# Second iteration

  - language: Ruby
  - delete existing code
  - swap pairs
    - TDD ping pong
    - evil pair
    - silent pairing
  - learnings:
    - work in a language at least one of you are familiar with
    - might not be the best scenario to learn a new language

# Lunch

  - …and there was lunch. Thanks [Leanintuit][leanintuit].

# Third iteration

  - language: Java
  - gdcr Toronto dare
    - Async Grid

      > Try to implement the game of life by representing each grid square as an asynchronous unit. Ensure every generation is computed independently.

  - learnings:
    - factory methods

# Fourth iteration

  - back to Ruby
  - gdcr Toronto dare
    - No Conditionals

      > Try to implement the game of life without branching or conditionals.
      > Think about it entirely as a sequence of transformations.
      > That means no if, switch, case, cond, when, unless, ternary operators, or similar. You can use pattern matching outside of the above.

  - learnings:
    - cell centric design

# Fifth iteration

  - mob programming
  - versus tandoori
  - learnings
    - conflicting (design) ideas may arrise; that is OK
    - explore ways to mitigate conflict

# All in all…

To quote Alex, it was great to see so many people that care about what they are doing come together and invest a full Saturday into honing their craft, learning with and from each other, and becoming better.

## _What's an interesting learning you're taking away from today?_

That having and environment set up and ready for development can be as easy as knowing which web app to use—[cyber‑dojo.org][cyber-dojo]

## _What from what you've learned today will you use at work, on Monday?_

From all the pair programming constraints we experimented with and learned about at this Code Retreat, I'm curious to experiment some more with the _evil pair_ approach. What is it? Continue with the ping pong pairing, but when it's your turn to write the implementation, make the tests pass, yet in a way that's different from what your pair intended. I can see this exercise help improve the level of specificity of rhe code, and the way we convey intent. If the code we have now leaves room for misunderstanding, let's surface it.

  [gdcr16-toronto]: http://coderetreat.org/events/global-day-of-coderetreat-2016-toronto-canada
  [cyber-dojo]: http://cyber-dojo.org/
  [leanintuit]: http://leanintuit.com/
