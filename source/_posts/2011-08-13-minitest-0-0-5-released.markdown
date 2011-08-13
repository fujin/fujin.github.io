---
layout: post
title: MiniTest Cookbook 0.0.5 Released
date: 2011-08-13 14:46
comments: true
categories: devops opschef lwrp minitest
---

A few days ago I released version 0.0.5 of the [MiniTest
Cookbook](https://github.com/fujin/minitest-cookbook) which included a
feature I've been working on that allows you to build MiniTest unit
test cases programmaticaly throughout the Chef run and have them
executed by a dedicated test runner.

``` ruby
    minitest_unit_testcase "test something" do
      block do
        refute_equal true, false, "true is not false"
        assert_equal true, true, "true is not equal true
      end
    end
```

I've also retained the ability to run via Vagrant, for ease of
testing. The [develop branch](https://github.com/fujin/minitest-cookbook/tree/develop) has some more recent enhancements, notably
an in-line Sinatra server which I'm building example test cases
around.

<pre>
vagrant@vagrant:~$ sudo chef-solo -c /tmp/vagrant-chef-2/solo.rb -j
/tmp/vagrant-chef-2/dna.json
[Sat, 13 Aug 2011 03:03:58 +0000] INFO: *** Chef 0.10.2 ***
[Sat, 13 Aug 2011 03:03:58 +0000] INFO: Setting the run_list to
["recipe[minitest]", "recipe[minitest::examples]"] from JSON
[Sat, 13 Aug 2011 03:03:58 +0000] INFO: Run List is [recipe[minitest],
recipe[minitest::examples]]
[Sat, 13 Aug 2011 03:03:58 +0000] INFO: Run List expands to [minitest,
minitest::examples]
[Sat, 13 Aug 2011 03:03:58 +0000] INFO: Starting Chef Run for
vagrant.monkeybrains.net
[Sat, 13 Aug 2011 03:03:58 +0000] INFO: Processing
gem_package[minitest] action install (minitest::default line 19)
[Sat, 13 Aug 2011 03:04:08 +0000] INFO: gem_package[minitest]
installed version ~> 2.3.1
[Sat, 13 Aug 2011 03:04:08 +0000] INFO: Processing
gem_package[net-tftp] action install (minitest::default line 24)
[Sat, 13 Aug 2011 03:04:08 +0000] INFO: Chef Handlers will be at:
/tmp/handlers
[Sat, 13 Aug 2011 03:04:08 +0000] INFO: Processing
remote_directory[/tmp/handlers] action create (chef_handler::default
line 23)
[Sat, 13 Aug 2011 03:04:08 +0000] INFO: Processing
cookbook_file[/tmp/handlers/README] action create (dynamically
defined)
[Sat, 13 Aug 2011 03:04:08 +0000] INFO:
remote_directory[/tmp/handlers] created
[Sat, 13 Aug 2011 03:04:08 +0000] INFO: Processing
cookbook_file[/tmp/handlers/chefminitest.rb] action create
(minitest::default line 28)
[Sat, 13 Aug 2011 03:04:08 +0000] INFO: Processing
chef_handler[ChefMiniTest::Handler] action enable (minitest::default
line 29)
[Sat, 13 Aug 2011 03:04:08 +0000] INFO: Enabling
chef_handler[ChefMiniTest::Handler] as a report handler
[Sat, 13 Aug 2011 03:04:08 +0000] INFO: Enabling
chef_handler[ChefMiniTest::Handler] as a exception handler
[Sat, 13 Aug 2011 03:04:08 +0000] INFO: Processing
gem_package[sinatra] action install (minitest::examples line 1)
[Sat, 13 Aug 2011 03:04:08 +0000] INFO: Processing
gem_package[minitest] action install (minitest::default line 19)
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: gem_package[minitest]
installed version ~> 2.3.1
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Processing
gem_package[net-tftp] action nothing (minitest::default line 24)
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Processing
remote_directory[/tmp/handlers] action nothing (chef_handler::default
line 23)
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Processing
cookbook_file[/tmp/handlers/chefminitest.rb] action create
(minitest::default line 28)
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Processing
chef_handler[ChefMiniTest::Handler] action nothing (minitest::default
line 29)
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Processing
gem_package[sinatra] action nothing (minitest::examples line 1)
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Processing
minitest_unit_testcase[test_truth] action create (minitest::examples
line 5)
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Processing
minitest_unit_testcase[test_http_port] action create
(minitest::examples line 55)
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Processing
minitest_unit_testcase[test_http_fitter_happier] action create
(minitest::examples line 55)
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Processing
minitest_unit_testcase[test_dns_resolution] action create
(minitest::examples line 55)
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Processing
minitest_unit_testcase[test_tftp_binary_get] action create
(minitest::examples line 55)
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Chef Run complete in
10.741559696 seconds
[Sat, 13 Aug 2011 03:04:09 +0000] INFO: Running report handlers
Run options: -v --seed 59848

# Running tests:

[Sat, 13 Aug 2011 03:04:09 +0000] INFO: ChefMiniTestRunner starting
#<Class:0x00000004570568>#test_truth = 0.00 s = .
#<Class:0x00000004570400>#test_http_port = 0.00 s = E
#<Class:0x00000004570298>#test_http_fitter_happier = 0.00 s = E
#<Class:0x00000004570130>#test_dns_resolution = 0.03 s = E
#<Class:0x0000000456ffc8>#test_tftp_binary_get = 5.03 s = E
[Sat, 13 Aug 2011 03:04:14 +0000] INFO: ChefMiniTestRunner completed


Finished tests in 5.063267s, 0.9875 tests/s, 0.1975 assertions/s.

  1) Error:
test_http_port(#<Class:0x00000004570400>):
Errno::ECONNREFUSED: Connection refused - connect(2)
    /usr/local/lib/ruby/1.9.1/socket.rb:37:in `connect'
    /usr/local/lib/ruby/1.9.1/socket.rb:37:in `connect_internal'
    /usr/local/lib/ruby/1.9.1/socket.rb:86:in `connect'
    /usr/local/lib/ruby/1.9.1/socket.rb:247:in `block in tcp'
    /usr/local/lib/ruby/1.9.1/socket.rb:157:in `each'
    /usr/local/lib/ruby/1.9.1/socket.rb:157:in `foreach'
    /usr/local/lib/ruby/1.9.1/socket.rb:239:in `tcp'
    /tmp/vagrant-chef-2/chef-solo-0/minitest/recipes/examples.rb:18:in
    `block in from_file'

  2) Error:
test_http_fitter_happier(#<Class:0x00000004570298>):
Errno::ECONNREFUSED: Connection refused - connect(2)
    /usr/local/lib/ruby/1.9.1/net/http.rb:644:in `initialize'
    /usr/local/lib/ruby/1.9.1/net/http.rb:644:in `open'
    /usr/local/lib/ruby/1.9.1/net/http.rb:644:in `block in connect'
    /usr/local/lib/ruby/1.9.1/timeout.rb:44:in `timeout'
    /usr/local/lib/ruby/1.9.1/timeout.rb:89:in `timeout'
    /usr/local/lib/ruby/1.9.1/net/http.rb:644:in `connect'
    /usr/local/lib/ruby/1.9.1/net/http.rb:637:in `do_start'
    /usr/local/lib/ruby/1.9.1/net/http.rb:626:in `start'
    /usr/local/lib/ruby/1.9.1/net/http.rb:1168:in `request'
    /usr/local/lib/ruby/gems/1.9.1/gems/chef-0.10.2/lib/chef/rest/rest_request.rb:84:in
    `block in call'
    /usr/local/lib/ruby/gems/1.9.1/gems/chef-0.10.2/lib/chef/rest/rest_request.rb:99:in
    `hide_net_http_bug'
    /usr/local/lib/ruby/gems/1.9.1/gems/chef-0.10.2/lib/chef/rest/rest_request.rb:83:in
    `call'
    /tmp/vagrant-chef-2/chef-solo-0/minitest/recipes/examples.rb:30:in
    `block (3 levels) in from_file'
    /usr/local/lib/ruby/1.9.1/timeout.rb:58:in `timeout'
    /tmp/vagrant-chef-2/chef-solo-0/minitest/recipes/examples.rb:29:in
    `block (2 levels) in from_file'
    /tmp/vagrant-chef-2/chef-solo-0/minitest/recipes/examples.rb:26:in
    `each'
    /tmp/vagrant-chef-2/chef-solo-0/minitest/recipes/examples.rb:26:in
    `block in from_file'

  3) Error:
test_dns_resolution(#<Class:0x00000004570130>):
Errno::ECONNREFUSED: Connection refused - recvfrom(2)
    /usr/local/lib/ruby/1.9.1/resolv.rb:747:in `recv'
    /usr/local/lib/ruby/1.9.1/resolv.rb:747:in `recv_reply'
    /usr/local/lib/ruby/1.9.1/resolv.rb:641:in `request'
    /usr/local/lib/ruby/1.9.1/resolv.rb:506:in `block in
    each_resource'
    /usr/local/lib/ruby/1.9.1/resolv.rb:1000:in `block (3 levels) in
    resolv'
    /usr/local/lib/ruby/1.9.1/resolv.rb:998:in `each'
    /usr/local/lib/ruby/1.9.1/resolv.rb:998:in `block (2 levels) in
    resolv'
    /usr/local/lib/ruby/1.9.1/resolv.rb:997:in `each'
    /usr/local/lib/ruby/1.9.1/resolv.rb:997:in `block in resolv'
    /usr/local/lib/ruby/1.9.1/resolv.rb:995:in `each'
    /usr/local/lib/ruby/1.9.1/resolv.rb:995:in `resolv'
    /usr/local/lib/ruby/1.9.1/resolv.rb:498:in `each_resource'
    /usr/local/lib/ruby/1.9.1/resolv.rb:391:in `each_address'
    /usr/local/lib/ruby/1.9.1/resolv.rb:367:in `getaddress'
    /tmp/vagrant-chef-2/chef-solo-0/minitest/recipes/examples.rb:38:in
    `block in from_file'

  4) Error:
test_tftp_binary_get(#<Class:0x0000000456ffc8>):
Net::TFTPTimeout: execution expired
    /usr/local/lib/ruby/gems/1.9.1/gems/net-tftp-0.1.0/lib/net/tftp.rb:150:in
    `recvfrom'
    /usr/local/lib/ruby/gems/1.9.1/gems/net-tftp-0.1.0/lib/net/tftp.rb:150:in
    `block (2 levels) in getbinary'
    /usr/local/lib/ruby/gems/1.9.1/gems/net-tftp-0.1.0/lib/net/tftp.rb:149:in
    `loop'
    /usr/local/lib/ruby/gems/1.9.1/gems/net-tftp-0.1.0/lib/net/tftp.rb:149:in
    `block in getbinary'
    /usr/local/lib/ruby/gems/1.9.1/gems/net-tftp-0.1.0/lib/net/tftp.rb:148:in
    `getbinary'
    /tmp/vagrant-chef-2/chef-solo-0/minitest/recipes/examples.rb:49:in
    `block (3 levels) in from_file'
    /usr/local/lib/ruby/1.9.1/timeout.rb:58:in `timeout'
    /tmp/vagrant-chef-2/chef-solo-0/minitest/recipes/examples.rb:48:in
    `block (2 levels) in from_file'
    /usr/local/lib/ruby/1.9.1/tempfile.rb:320:in `open'
    /tmp/vagrant-chef-2/chef-solo-0/minitest/recipes/examples.rb:47:in
    `block in from_file'

5 tests, 1 assertions, 0 failures, 4 errors, 0 skips
[Sat, 13 Aug 2011 03:04:14 +0000] INFO: Report handlers complete
vagrant@vagrant:~$
</pre>

Test all the fucking time.
