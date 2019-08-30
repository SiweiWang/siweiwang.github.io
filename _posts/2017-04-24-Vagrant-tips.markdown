---
layout: post
title:  "Vagrant kick start tips"
date:   2017-04-24 09:40:49 -0500
categories: DevOps
tags: [DevOps, Vagrant]
---
# Kick start with vagrant

[Vagrant](https://www.vagrantup.com/intro/index.html) is popular tool used for helping local development and testing. Here are some tips that has been very useful for me when setting up and using Vagrant

## Use box file

A common practice when using vagrant is to use shell script to provision the vm, for example:

in Vagrantfile

{% highlight ruby %}
vm4.vm.provision "shell", path: "VagrantProvision/provision_centos7.sh"
{% endhighlight %}

in provision_centos7.sh

{% highlight Bash shell scripts %}

#!/bin/bash
set -x

sudo rpm -iUvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum update -y && sudo yum install -y net-tools wget ansible python-docker-py python-prettytable

# More setup ...

{% endhighlight %}

While This is helpful to make sure the changes made to the base image are versioned and can be share within teams, it can be slow when a base box needs to get build every time.

A better way would be to build the box file which just need to build once which will speed up the vm start up time significantly.

First, let's define our base image in Vagrantfile:

Here we use centos 7 as an example:

{% highlight ruby %}

config.vm.define "base" do |base|
  config.vm.provider :virtualbox do |vb|
    vb.name = "base"
  end
  base.vm.box = "centos/7"
  # config local network
  base.vm.network "private_network", ip: "192.168.3.9"
  base.vm.provision "shell", path: "VagrantProvision/provision_centos7.sh"
end

{% endhighlight %}

Sample script for building box file :

{% highlight Bash shell scripts %}

echo "creating dir to hold box file ..."
mkdir Boxes

echo "making sure base vm is down ..."
vagrant destroy -f base

echo "starting base vm ..."
vagrant up base

echo "starting cleaning up old box file ..."
rm -f Boxes/centos7.box
vagrant box remove --force  file://Boxes/centos7.box

echo "packing the new image into Boxes/centos7.box"
vagrant package --base base -o Boxes/centos7.box

echo "cleaning up base vm ..."
vagrant destroy -f base

{% endhighlight %}

After the box file is build, change the vagrant file to refer to the local box file:

{% highlight ruby %}
vm1.vm.box  = "file://Boxes/centos7.box"
{% endhighlight %}

Here we can skip the provisioning step when start the vm since all the changes needed as pre-build in the box file


## Set up http proxy for vm

When running vagrant in the enterprise network, a web proxy is usually needed to access public internet.

A easy to setup proxy for the vms in vagrant is to use [vagrant-proxyconf](http://tmatilai.github.io/vagrant-proxyconf/)

Following code will get the proxy setting set in current environment variable and apply them to the vm

First of all, install the plugin

{% highlight Bash shell scripts %}

vagrant plugin install vagrant-proxyconf

{% endhighlight %}

in Vagrantfile

{% highlight ruby %}

  if Vagrant.has_plugin?("vagrant-proxyconf")
    config.proxy.http     = ENV['http_proxy']

    config.proxy.https    = ENV['https_proxy']

    config.proxy.no_proxy = ENV['no_proxy']
  end
{% endhighlight %}

Current proxy setting will set proxy for apt/yum, docker, git, npm if they are installed on the box.

## Separate vm definition from Vagrant code

## Use vagrant cacher to speed up provision time
