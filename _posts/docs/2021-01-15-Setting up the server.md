---
layout: post
category: documentation
title: "Setting up the Server"
date: 2021-01-15
---

We decided on a server hosted by Amazon Web Services (AWS).

Amazon for a 12 month trial period offers a free server with certain specifications. These are (as the time of writing) (nearly) enough for our purposes. We only had to add additional disk space, as the 30 GB were not sufficient for all the game files. Currently we have upgraded to 60 GB which cost about 3 US$ a month.

## Basic installation ##

This [link](https://www.reddit.com/r/GlobalOffensive/comments/hro0ct/setting_up_your_own_dedicated_cs_go_community/) takes you to "a step by step guide on How to set up your own dedicated CS: GO community server using AWS for free. Don't worry. It's a noob's guide." 

It explains in detail
* the required Pre-requisites for setting up the server 
* creating a (Ubuntu) linux server using Amazon’s EC2 services 
* logging into that server using ssh
* installing steam and Counter Strike : Global Operations
* creating a game token for your dedicated server
* adding the game to the servers auto start procedure

As a result you will have your very own dedicated CS:GO server. 

**Attention**: before the steam installation we had to execute the following line to add some missing packages.

    sudo apt install libsdl2-2.0-0:i386

Additional pre-requisites are
* you must own a credit card to create an AWS account.
* you must have a least a passing aquaintance with using a console window (like windows command prompt).
* you may have to increase the disk size before being able to install CS:GO (see next chapter).

## Increasing disk space ##
The above guide will give you an installation which in our case used nearly 29 GB of the server’s initially available 30 GB disk space. This is not enough to install updates or additional maps – and may in the near future not be enough to even install the game. For this reason you **will** have to increase the disk space available. Increasing it to 50 GB should be sufficient. 

Note: Increasing the disk space will cost you about 0,1 US$ per GB a month. For exact pricing look for Amazon Elastic Block Store (EBS) costs.

To increase your server’s disk space you have to 
* add more disk space to the instance
* assign that disk space to the hard drive’s configuration.

To add disk space to your instance

* In AWS Services select EC2 (dashboard), click on your instance and you should see the details panel displayed.
* In the details panel click on “Storage” and there on the (single) volume of your instance
* On the next page click on “Actions” and select “Modify Volume”
* A window pops up where you can change the size of the volume. Beware that sizes above 30GBs will cost you a monthly fee.
* After clicking on “Modify” the new space will be assigned to your instance.

To be able to use the increased disk space in your server’s file system you also have to extend the linux file system. This [link](https://aws.amazon.com/de/premiumsupport/knowledge-center/extend-linux-file-system/) explains how to do that.

Notes

* Your file system should be an ext4 file system as used in the linked text.
* If after the `resize2fs` command the disk size seems not to be increased reboot the server and check again.
  
To reboot the server use

	sudo shutdown -r now

where now shuts down immediately and -r reboots the server. 

## Adding a permanent IP address ##

Every time you reboot the server it will get a new IP address. If you do not want to tell your friends the new IP after every reboot you should either use a dynamic DNS service or AWS Elastic IP service. 

We decided to use the Elastic IP service because it is so easy to install. 

**Be aware**: the Elastic IP service is only for free as long as the server is running. Once you shut down the server for more than an hour Amazon will charge you for this “unused” address. It's only about 0,1US$ a day but of course this will add up.

In our case as we are using Amazon’s free server it does not matter if we keep the server up 24/7. One we upgrade the server we will have to decide to either pay the price for keeping it running or to pay the fee for a “sleeping” server or to switch to a dynamic DNS.

To activate the Elastic IP address for your server

* In AWS Services select EC2 (dashboard) and in the “Resources” section there should be a listing for “Elastic IPs”. 
* Click on the link and in the next page select “Allocate Elastic IP address”
* In the next page just leave everything as it is and click on “Allocate”.

You now have a permanent IP address for your server.
