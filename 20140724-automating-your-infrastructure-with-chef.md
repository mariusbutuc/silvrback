Tonight, the DevOps Toronto group met with [Bakh Inamov][bakh] from Chef to talk about [Automating your in infrastructure with Chef][meetup] -- the previous two mentions of Chef do mention different entities, since as I've learnt tonight, OpsCode has been rebranded as Chef for a while now.

The rough agenda soon turned into an open discussion on everything and anything Chef. From the big picture of the Chef ecosystem to specifics like how do you use Chef in AWS, how about on Windows or on Azure or Vagrant, focus on testing recipes and cookbooks, how are the Chef client and server roles architected, and what are the strategies in use, security, Chef vs. Puppet or Ansible, does CFEngine still exist, and many more.

Tonight is the first time I've heard about [chef-metal][metal], first from [Leons][leons] when he described some details on how they use it in an internal project at IBM, and then from Bakh, when it came up in the open discussion. I both like the problem it comes to solve very much, and I miss working at the scale where you need a tool like it. Still, the question if anybody uses metal in production was arguably left unanswered.

The [Chef Supermarket][supermarket] is another good piece of news. One of the best metrics of quickly eyeballing the quality of cookbook still seems to be the number of downloads. And if you still remember the Community, that the Supermarket now replaces, that counter was only incrementing every time someone would actually click the download button. The Supermarket on the other hand plugs into every API call, and now tracks all the installs from both chef-client and chef-solo. There are many other cool details about it, but I'll mention only one more: [it's open source][sm-os]! 

Another takeaway I like is this slide Bakh pulled out at one point:

![the Chef Workflow with Tools](https://silvrback.s3.amazonaws.com/uploads/39c28bd5-fe31-4f81-9612-ebd217c43457/IMG_20140724_222729_large.jpg)

Just so you don't have to squint too hard, here is the described Chef workflow, with tools:

1. Create new skeleton cookbook
  * Cookbook package manager (Berkshelf)
  * Revision control system (Git)
2. Create a VM environment for cookbook development
  * VM manager (Vagrant)
  * Ruby package manager (bundler)
3. Write/debug cookbook recipes _(iterative step)_
  * Source editor (Sublime etc.)
  * Revision control system (Git)
  * Ruby static checking (rubocop)
  * Chef static checking (foodcritic)
  * Chef unit testing (chefspec)
  * Integration tests (RSpec, BATS etc.)
  * Test harness (test-kitchen)
  * Chef local execution (chef-solo, chef-zero)
4. Perform acceptance tests
  * Test harness (test-kitchen)
  * Acceptance tests (Gherkin, Cucumber, Leibniz)
  * Chef server environment (Enterprise Chef, which includes Chef Analytics)
5. Deploy to production
  * Chef server environment (Enterprise Chef, which includes Chef Analytics)

It was fun meeting with Bakh and the DevOps Toronto group. It was fun talking about Chef's learning curve now vs. two years ago, hearing about other people sharing similar Chef struggles back in the day, what did the community learn out of it and how did things improve till today. 

Next? [DevOps Days Toronto][dd-to], maybe?


  [bakh]: https://twitter.com/bahman2000
  [meetup]: http://meetu.ps/2myJRT
  [metal]: https://github.com/opscode/chef-metal
  [leons]: https://twitter.com/leonsp
  [supermarket]: https://supermarket.getchef.com
  [sm-os]: https://github.com/opscode/supermarket
  [learn-chef]: http://learn.getchef.com
  [dd-to]: http://devopsdays.org/events/2014-toronto/
