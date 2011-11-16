---
layout: post
title: "Pennyworth Quick Start"
date: 2011-11-16 14:17
comments: true
categories: devops opschef jenkins ruby
---

At [Heavy Water operations](http://www.hw-ops.com/) we have been
working on a Jenkins based continuous deployment system we've named
[Pennyworth](https://github.com/heavywater/pennyworth). Should you choose to evaluate pennyworth, any project you
desire can be automatically tested, packaged and included in an appropriate repository
available for automated installation and deployment with APT
(hopefully via Chef!)

This post will hopefully get you up and running in a
virtual environment, building the example jobs and walking through
each of the keys required to create a new job.

<!--more-->

## Requirements
- [Ruby](http://www.ruby-lang.org/en/) versions: 1.9.3-p0, 1.9.2-p290, 1.8.7-p352
- [VirtualBox 4.1.6](https://www.virtualbox.org/wiki/Downloads)
- [VirtualBox 4.1.6 Extension pack](http://download.virtualbox.org/virtualbox/4.1.6/Oracle_VM_VirtualBox_Extension_Pack-4.1.6-74713.vbox-extpack)
- 4GB RAM (tunable in Vagrantfile)

### Clone the repository
I'm using /tmp in this example.

<pre>
haxstation :: /tmp » hub clone heavywater/pennyworth
Cloning into pennyworth...
remote: Counting objects: 298, done.
remote: Compressing objects: 100% (179/179), done.
remote: Total 298 (delta 72), reused 295 (delta 69)
Receiving objects: 100% (298/298), 114.21 KiB, done.
Resolving deltas: 100% (72/72), done.
</pre>

### Installation of dependencies
Now that you have a clone you can begin installing the dependencies
with [Bundler](http://gembundler.com):

<pre>
haxstation :: /tmp/pennyworth ‹develop› » bundle install
Fetching source index for http://rubygems.org/
Installing archive-tar-minitar (0.5.2)
Installing erubis (2.7.0)
Installing ffi (1.0.10) with native extensions
Installing i18n (0.6.0)
Installing json (1.5.2) with native extensions
Installing net-ssh (2.1.4)
Installing net-scp (1.0.4)
Installing thor (0.14.6)
Installing virtualbox (0.9.2)
Installing vagrant (0.8.7)
Using bundler (1.0.21)
Your bundle is complete! Use `bundle show [gemname]` to see where a
bundled gem is installed.
haxstation :: /tmp/pennyworth ‹develop› »
</pre>

### Tune Vagrant

With the dependencies all installed, you're ready to kick off
Vagrant. If you don't have 4GB of ram installed, open up the
`Vagrantfile` and edit line #4, reducing the `vm.memory_size` value:

``` ruby
    Vagrant::Config.run do |config|
      config.vm.define :pennyworth do |pennyworth|
      pennyworth.vm.customize do |vm|
        vm.memory_size = 4096
```

### Vagrant time

You're ready to launch Pennyworth! 

It takes 3000s+ on my [very hefty development workstation](http://valid.canardpc.com/show_oc.php?id=2081675):

- Intel i7-2600k @ 4.9 GHz (HT/WC)
- 16GB RAM (Vagrant configured to use 8192MB)
- Intel 510 SSD

Testing on EC2 has been much quicker due to bandwidth available provided you can afford the resource.

#### vagrant status
<pre>
haxstation :: /tmp/pennyworth ‹develop› » bundle exec vagrant status
Current VM states

pennyworth               not created

The environment has not yet been created. Run `vagrant up` to
create the environment.
</pre>

#### vagrant up
<pre>
haxstation :: /tmp/pennyworth ‹develop› » bundle exec vagrant up
[pennyworth] Importing base box 'natty64'...
[pennyworth] Matching MAC address for NAT networking...
[pennyworth] Clearing any previously set forwarded ports...
[pennyworth] Forwarding ports...
[pennyworth] -- ssh: 22 => 2222 (adapter 1)
[pennyworth] -- web: 8080 => 8080 (adapter 1)
[pennyworth] Creating shared folders metadata...
[pennyworth] Running any VM customizations...
[pennyworth] Booting VM...
[pennyworth] Waiting for VM to boot. This can take a few minutes.
[pennyworth] VM booted and ready for use!
[pennyworth] Mounting shared folders...
[pennyworth] -- v-root: /vagrant
[pennyworth] -- v-csc-1: /tmp/vagrant-chef-1/chef-solo-1
[pennyworth] -- v-csr-2: /tmp/vagrant-chef-1/chef-solo-2
[pennyworth] -- v-csdb-3: /tmp/vagrant-chef-1/chef-solo-3
[pennyworth] Running provisioner: Vagrant::Provisioners::ChefSolo...
[pennyworth] Generating chef JSON and uploading...
[pennyworth] Running chef-solo...
...
</pre>

<pre>
[pennyworth] [Wed, 16 Nov 2011 02:43:33 +0000] INFO: Chef Run complete
in 3482.654154 seconds
[pennyworth] [Wed, 16 Nov 2011 02:43:33 +0000] INFO: Running report
handlers
[pennyworth] [Wed, 16 Nov 2011 02:43:33 +0000] INFO: Report handlers
complete
haxstation :: /tmp/pennyworth ‹develop› »
</pre>

**3482 seconds = 58.0333333 minutes**

You'll want to avoid doing a full `vagrant destroy; vagrant up` cycle
very often, unless you have a very fast connection.

### Jenkins Dashboard
{% img /images/jenkins.png %}

### Reprepro index
{% img /images/reprepro.png %}

### Adding a pennyworth job with data bags
Here's the `ruby` pennyworth job data bag included as an example:
{% include_code ruby.js %}

Let's look at each of the keys.

#### version (*line 2 - 5*)


  This key contains a hash that Pennyworth uses to construct a version
  number automatically, available to shell scripts during the build
  process as `$VERSION`.

  In this example, `$VERSION` would be `1.9.3.$BUILD_NUMBER`

#### project_type (*line 6*)

  Controls which type of project pennyworth thinks this data bag item
  actually is.

  In this example, the project type is *Package from Git*, signifying
  that we will be continuously packaging a Git repository.

  Other available project types are: `test_from_git` which tests
  without packaging and `package` which lacks the Git support -- we
  have used it to install legacy packages only available over HTTP.

  This really only exists because a suitable abstraction hasn't been
  built around the jenkins_job LWRP yet, which we're planning on
  addressing shortly. Currently we pull all values out of the data bag
  and assign them (if available). [It's pretty gross](https://github.com/heavywater/pennyworth/blob/develop/cookbooks/pennyworth/recipes/default.rb#L71).

#### git_branch (*line 7*)
  
  Used for the `*_from_git` project types; controls which branch Jenkins
  will track for changes.

#### git_url (*line 8*)

  Used for the `*_from_git` project types; controls which remote Jenkins
  will track for changes.

#### test_commands (*line 9 - 13*)
  
  An array of shell commands to run to start the testing off from a
  fresh checkout, so, potentially including autoconf and
  configure... Obviously hard to provide a catch all for this.

  We have been considering an advanced build tool that can determine
  any and all appropriate commands required to kick this phase off.

#### build_commands (*line 14 - 16*)

  An array of shell commands to run after testing to produce a
  compiled version of your project.

  Bundler could be used during this phase to make pre-built Gem
  extensions, etc. available to the packaging phase. We've used this
  technique for Rails deployments in the past. Consequently, this is
  where build tools like Make, Rake and Leiningen have been used to
  produce compiled versions of many of our clients applications.

  Our example uses the `$DESTDIR` variable, which is generated by
  Jenkins as `$WORKSPACE/fpm`; i.e. a sub directory in the
  workspace. This variable is available to all project types.

#### package_commands (*line 17 - 23*)

  An array of shell commands to run after building a compiled version
  of the project.

  Our example shows the steps required to package a directory with
  [FPM](https://github.com/jordansissel/fpm) and include them into the
  built in APT repository managed by [Reprepro (previously known as mirrorer)](http://mirrorer.alioth.debian.org/)

  It also makes use of our [update_version.rb](https://github.com/heavywater/pennyworth/blob/develop/cookbooks/pennyworth/files/default/update_version.rb) script which uses the
  Chef::DataBagItem class to update an item in the *package*
  data-bag. We use this during deployment to figure out what versions
  of packages have been built by pennyworth.

  This would be another great extension point for the build tool I
  hinted at earlier that could automatically determine what kind of
  files should be included into packages and manage the inclusion of
  the artifact in a repository.

  We have used this section to launch notification and deployment
  tools that signal Chef to run over the network, including via SSH
  with the [Knife Deploy plugin](https://github.com/heavywater/pennyworth/blob/develop/.chef/plugins/knife/deploy.rb)
  which should be available as a gem soon.
#### child_projects (*line 24 - 26*)
  
  An array of job names that this job is a parent of. Used to create
  dependencies/triggers between jobs.

#### remote_poll (*line 27*)
  
  Enable or disable remote polling of the Git repository.

  Setting this to false means the job will not poll Git, but can be
  triggered by API or the Web UI.

#### clean (*line 28*)

  Enable or disable the cleaning of the workspace.

#### wipeoutworkspace (*line 29*)

  Enable or disable the "wiping out" of the workspace, total
  removal. Probably not required with clean.

### Questions?

Contact [AJ](https://twitter.com/fujin_) <aj@junglist.gen.nz> and
[Darrin](https://twitter.com/dje) <darrin@heavywater.ca>

[Heavy Water operations](http://www.hw-ops.com/index.html) has
sponsored and requisitioned the development, testing and
deployment of Pennyworth. Feel free to contact us to discuss more
complicated requirements, or any problems. No warranty implied =)
