# Whiteboarding the LHC

At RailsConf earlier this year, James Edward Gray II talked about [coding interviews, and how to pass them][whiteboard-lhc]:

> If you apply for a programming job, you may be asked to complete a take home code challenge, "pair program" with another developer, and/or sketch out some code on a whiteboard. A lot has been said of the validity and fairness of these tactics, but, company ethics aside, what if you just need a job? In this talk, I'll show you a series of mistakes I have seen in these interview challenges and give you strategies for avoiding them. I'll give recommendations for how you can impress the programmers grading your work and I'll tell you which rules you should bend in your solutions.

Watched [the recording][whiteboard-video] as soon as it became available, and drew inspiration for the interviews I conducted within Shopify. Some ideas felt like common sense, and some became common sense with time, yet there were still a few that I wished they were part of my common sense today.

For the ones I was still wishing, I watched it again, this time jotting down some notes. _Disclaimer:_ almost every word you find below can be either read in the [slide deck][whiteboard-deck] or heard in [the recording][whiteboard-video]. Mine is only the selection of notes, and their organization in a mind map. So, here we go:

## meta

  ![meta][mindmap-01-meta]

  - about
    - coding interviews
    - how to pass them
  - types
    - take home
      - with time limits
      - without time limits
    - technical interview
      - pairing
      - whiteboard
    - auditions
  - At NoRedInk
    - 75% of candidates fail the take home
    - 30% of the rest fail the first technical interview

## How can you prepare?

  ![How can you prepare?][mindmap-02-prepare]

  - study
    - a certain subset of programming knowledge
      - concepts
        - big O notation
          - [bigocheatsheet.com][bigocheatsheet]
          - metric used to describe an upper bound on algorithm efficiency
          - can be used for time / space
          - basic math
            - drops constants and non-dominant terms
            - adds independent work
            - multiplies dependent work
          - can help you solve problems: faster & easier
        - memoization
          - coming at the problem from the top-down, with caching
        - dynamic programming
          - coming from the bottom-up
          - building up to the correct answer
        - recursion
          - linear algorithm: O(n)
          - recursive algorithms: O(branches^depth)
        - bit manipulation
      - data structures
        - hash tables
        - trees / tries / graphs
        - linked lists
        - heaps
        - stacks and queues
      - algorithms
        - binary search
        - bread-first search / depth-first search
        - merge sort / quicksort
    - probably not the one you use daily
    - helps to have it fresh in your brain
  - problems
    - algorithms
      - [Fibonacci][fibonacci]
        - recursive
        - memoized
        - dynamic
      - two sorted arrays; find elements in common
        - brute force
        - intermediate
        - linear
    - brain teasers
      - Nine Balls
        - binary search
        - merge sort
    - Ruby can sometimes help
      - `Minitest::Benchmark`
        - `assert_performance_constant`
        - `assert_performance_exponential`
        - `assert_performance_linear`
        - `assert_performance_logarithmic`
        - `assert_performance_power`
  - practice
    - [exercism.io][exercism]
      - consistently solve one in less than an hour
    - [rubyquiz.com][rubyquiz]
    - [Cracking the Coding Interview: 189 Programming Questions and Solutions][cracking]

## Open Source

  ![Open Source][mindmap-03-open-source]

  - A small gem is fine
  - most important details are
    - strong README
    - clean code
    - small methods
    - tests
    - documentation

## What to do while coding?

  ![What to do while coding?][mindmap-04-coding]

  - main goal
    - not to be seen as a bad hire
    - good hires are good
    - bad hires are really bad
      - not only the performance of the bad hire itself
      - drag down the performance of other employees
      - like a multiplier
  - avoid making major mistakes
    - read carefully
      - ask as many clarifying questions as needed
    - follow directions
    - stop and think
      - use 5 minutes to make a plan
      - use the rest of your time better
    - think out loud
      - My first thought is…
      - I'm considering the tradeoffs…
      - I might circle back to this…
      - I don't know.
    - don't make offensive comments
    - start at the hard part
      - don't begin by designing objects
      - jump straight to the interesting bit
      - fake input as needed
      - ignore UI completely
    - idea test the core
      - pretty much no one tests anything
    - avoid dependencies
      - and don't recreate them manually
    - write less code
      - don't "gold plate"
    - ignore "bonus" challenges
      - it's just not worth it
    - consider time limits soft
      - submit on time
      - [resubmit later][resubmit-later], if needed
        - knowing that you wanted to finish the problem
    - show off your text editing skills
      - know five editing shortcuts
      - make one multiple cursors / macros

## Everything connects

Now that we've gone through the parts, here's how everything connects, at least in my head:

![Everything connects][mindmap-05-all]

  [whiteboard-lhc]: http://railsconf.com/program#prop_1749
  [whiteboard-video]: http://confreaks.tv/videos/railsconf2016-implementing-the-lhc-on-a-whiteboard
  [whiteboard-deck]: https://speakerdeck.com/jeg2/implementing-the-lhc-on-a-whiteboard
  [bigocheatsheet]: http://bigocheatsheet.com/
  [fibonacci]: https://youtu.be/g5lWfHEeibs?t=13m30s
  [exercism]: http://exercism.io/
  [rubyquiz]: http://rubyquiz.com/
  [cracking]: https://www.amazon.ca/Cracking-Coding-Interview-Programming-Questions/dp/0984782850
  [resubmit-later]: https://youtu.be/g5lWfHEeibs?t=33m
  [mindmap-01-meta]: https://raw.githubusercontent.com/mariusbutuc/silvrback/master/2016-10-21/whiteboarding_the_lhc_01_meta.png
  [mindmap-02-prepare]: https://raw.githubusercontent.com/mariusbutuc/silvrback/master/2016-10-21/whiteboarding_the_lhc_02_prepare.png
  [mindmap-03-open-source]: https://raw.githubusercontent.com/mariusbutuc/silvrback/master/2016-10-21/whiteboarding_the_lhc_03_open_source.png
  [mindmap-04-coding]: https://raw.githubusercontent.com/mariusbutuc/silvrback/master/2016-10-21/whiteboarding_the_lhc_04_coding.png
  [mindmap-05-all]: https://raw.githubusercontent.com/mariusbutuc/silvrback/master/2016-10-21/whiteboarding_the_lhc_05_all.png
