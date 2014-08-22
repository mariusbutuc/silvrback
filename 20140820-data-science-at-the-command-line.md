![Data Science at the Command Line sb_float][book-cover] [Jeroen&nbsp;Janssens][jj]'s webcast on **[Data&nbsp;Science at the Command&nbsp;Line][webcast]** proved to be a mix between a good proof that the Unix command-line is not dead for Data&nbsp;Science and a more that two hours hands-on marathon. What made this webcast even more unique was the hands-on element (that more speakers could take example form). Jeroen built the [Data&nbsp;Science at the Command&nbsp;Line][dsatcl] website almost like a cheat-sheet of his book, but also leveraging his **[Data Science Toolkit][dst]** project. Following the clear instructions, the reader or audience member can either spin up a local virtual machine using Vagrant and VirtualBox or an AWS instance to follow along with all the necessary command-line tools already available, without having to go through the involved installation process of each.

The webinar is more of a bird's eye view over Jeroen's book with the same title and the sections in his deck are there to prove it. They bring an overview of all the chapters in the book that are currently available:
1. Introduction
2. Getting Started
3. Obtaining Data
4. Creating Reusable Command-line Tools
5. Scrubbing Data
6. ~~Managing Your Data Workflow~~ &mdash; _unavailable at the time of writing_
7. Exploring Data
8. Parallel Pipelines
9. ~~Modelling Data~~ &mdash; _unavailable at the time of writing_
10. Conclusion

So if you're wandering if the book with the same title, **Data&nbsp;Science at the Command&nbsp;Line**, is the book for you, you can get an answer in exchange for 2h of your time. I'm by far not a Unix CLI guru, but I was impressed with how fast and how much you can get done just by leveraging the Unix file system and a good toolbox of command-line utilities.

## Data Science is OSEMN

We've all heard it before, but this mnemonic will never grow old for me:

  * **O**btaining data
  * **S**crubing data
  * **E**xploring data
  * **M**odeling data
  * i **N**terpreting data

The intro was a beautifully orchestrated parade of Unix command line, that I believe can whet any Data Scientist's appetite. From using [`asciinema`][asciinema] to record and share your terminal sessions to using [**Rio**][rio] to load CSV as input into **R** as a `data.frame` and generate PNGs with **ggplot2**, the toolbox proved to be very diverse: some stock, some custom built, but all available in the open domain.

```
$ parralel -j1 --progress --delay 0.1 --results results \
    "curl -sL"

$ tree results | head

$ cat results/... \
    | jq '.response.docs[] | { date: .pub_date, ... }' \
    | json2csv -p -k date,type,title \
    > fashion.csv

$ type -a pwd

$ seq 100 \
    | grep 3 \
    | wc -l # word count that becomes line count

$ echo -n "Hello" > hello-world
$ echo " World" >> hello-world
$ cat hello-world
$ cat hello-world | wc -w

$ cp server.log{,.bak} # shell brace expansion

$ whereami
```

## Obtaining Data _(aka Chapter 03)_
  - Copying local files
  - Decompressing files

    ```
    $ touch logs-{01..10}.csv
    $ unpack # calls the appropriate extractor
    ```

  - Converting Microsoft Excel spreadsheets
    - `in2csv` and `csvlook`
  - Querying a DB
    - the classic `iris.db`, over-used in Machine Learning presentations

    ```
    $ sql2csv \
      --db 'sqlite:///data/iris.db' \
      --query 'SELECT * FROM iris WHERE sepal_length > 7.5' \
      | csvlook
    ```

  - Downloading from the Internet
    - our good old friend `curl` was mentioned again, as expected.

## Creating reusable command-line tools _(aka Chapter 04)_
  - UNIX Philosophy: write command-line tools that:
    - do one thing and do it well
    - work together
    - handle text streams

  - How?
    - making script executable
    - defining a shebang
    - adding parameters
    - command-line tools with Python and R
    - processing streaming data

  - Extracting most common words

    ```
    $ curl -s http://www.gutenberg.org/cache/epub/76/pg76.txt \
      | tr '[:upper:]' '[:lower:]' \
      | grep -oE '\w+' \
      | sort \
      | uniq -c \
      | sort -nr \
      | head -n 10
    ```

    - Turning a pipeline into a reusable command-line
      - move it into a text file
      - add execution permissions
         `$ chmod u+x top-words-1.sh`
      - add the shebang
         `#!/usr/bin/env/ bash`
      - use variables `$NUM_WORDS`

## Scrubbing Data _(aka Chapter 05)_
  - Scrubbing plain text
    - showcasing `head`, `sed`, `awk`, `tail` and `sample`
  - Scrubbing CSV
  - Working with JSON and XML
  - Executing SQL queries directly on CSV

## Exploring Data _(aka Chapter 07)_
  - Inspecting data
  - Feature names and Data types
  - Computing descriptive statistics
  - **R** from the command line using **Rio**
  - **Gnuplot** & **feedgnuplot** and **Rio** & **ggplot2**
  - Creating various plots

## Conclusions

[Gregory D. Horne][unix-not-dead] summarized it probably best, saying that:

> Fantastic presentation and further **proof the Unix command-line is not dead for data science**.

When it comes to the [Data&nbsp;Science at the Command&nbsp;Line][book] book, Jeroen did a great job whetting this novice Data Scientist's appetite with the webcast. I'm currently making progress on [Python for Data Analysis][python4da], but once done, Data&nbsp;Science at the Command&nbsp;Line will be next. And after that? Most likely [Thinking with Data][thinking-data], which Jeroen described as a good complement to his book.


  [jj]: https://twitter.com/jeroenhjanssens
  [webcast]: http://www.oreilly.com/pub/e/3115
  [recording]: https://twitter.com/OReillyMedia/status/502153174414024704
  [dsatcl]: http://datascienceatthecommandline.com/
  [dst]: http://datasciencetoolbox.org/

  [j@strata]: http://strataconf.com/stratany2014/public/schedule/detail/36204
  [book]: http://shop.oreilly.com/product/0636920032823.do
  [book-cover]: https://silvrback.s3.amazonaws.com/uploads/a0b2e15b-6db1-4a08-879b-ab563f13962b/dsatcl_sm_large.png

  [asciinema]: https://asciinema.org/
  [rio]: https://github.com/jeroenjanssens/data-science-at-the-command-line/blob/master/tools/Rio
  [unix-not-dead]: https://twitter.com/GregoryDHorne/status/502174780892905472
  [python4da]: http://shop.oreilly.com/product/0636920023784.do
  [thinking-data]: http://shop.oreilly.com/product/0636920029182.do

  [photo-cred]: https://www.flickr.com/photos/xurxosanz/14979264811/in/photostream/
