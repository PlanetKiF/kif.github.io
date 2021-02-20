---
layout: post
category: documentation
title: "Setting up the Server"
author: "Ciske Busch"
order: 05
date: 2021-02-20
---

We decided on setting up a server hosted by Amazon Web Services (AWS). This section explains how we did that, what changes we made to the initial setup and how much we are currently paying for it.

Amazon for a 12 month trial period offers a free server with certain specifications. These are (as the time of writing) (nearly) enough for our purposes. We only had to add additional disk space, as the 30 GB were not sufficient for all the game files. Currently we have upgraded to 60 GB which cost about 3 US$ a month.

## Basic installation ##

This [link](https://www.reddit.com/r/GlobalOffensive/comments/hro0ct/setting_up_your_own_dedicated_cs_go_community/) takes you to "a step by step guide on How to set up your own dedicated CS: GO community server using AWS for free. Don't worry. It's a noob's guide."

It explains in detail
* the required pre-requisites for setting up the server
* creating a (Ubuntu) linux server using Amazon’s EC2 services
* logging into that server using ssh
* installing steam and Counter Strike : Global Operations
* creating a game token for your dedicated server
* adding the game to the servers auto start procedure

As a result you will have your very own dedicated CS:GO server.

**BEFORE** you follow the guide linked above please read the following as it contains some hints which not knowing about has cost us some time.

* you must own a credit card to create an AWS account.
* you should have a least a passing acquaintance with using a console window (like windows command prompt).
* Instead of installing putty for a ssh connection with Windows 10 you can also use the in build OpenSSH client. Head to Settings > Apps and click “Manage optional features” under Apps & features. There you'll either find it is already  installed or you can add it by clicking on "Add a feature". Your %path% variable should then contain something like this `C:\WINDOWS\System32\OpenSSH\`
* we recommend to increase the disk size before installing CS:GO (see next chapter).
* Before the Steam installation we had to execute the following line to add some missing packages to the Ubuntu installation.

    sudo apt install libsdl2-2.0-0:i386


## Increasing disk space ##
The above guide will give you an installation which in our case used nearly 29 GB of the server’s initially available 30 GB disk space. This is not enough to install updates or additional maps – and may in the near future not be enough to even install the game. For this reason you **will** have to increase the disk space available to 50 GB (better to 60 GB). 

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
* If after the `resize2fs` command the disk size seems not to be increased as expected reboot the server and check again.

To reboot the server use

	sudo shutdown -r now

where now shuts down immediately and -r reboots the server.


## Adding a swap file ##

Our severs only has 1 GB of main memory. This currently is sufficient but with Linux there is the danger that the server crashes once it runs out of memory.

To adress this problem we added a swap file of 1 GB size. This [link](https://linuxbeast.com/tutorials/aws/how-to-add-swap-space-on-ec2-ubuntu-18-04/) explains how to do this.

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


## AWS CPU credits and credit usage ##

The following is a short summary of CPU credits and credit usage of an AWS hosted server. For more detailed information look [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-credits-baseline-concepts.html).

AWS provides you with servers with certain CPU specifiations but the price you pay for a server does NOT include the ability to let the CPU(s) run full speed 24/7. 

Instead an AWS instance has a CPU baseline with regard to it's maximum CPU power (T2.micro: 10%). If your CPU(s) run below that baseline they will accumulate CPU credits up to a given maximum (T2.micro: 144 credits). If they run above the baseline they will spend CPU credits. Our T2.micro servers's CPU runs at about 50% when we are playing and thus uses up available credits during that time. If all available CPU credits are spent two things can happen depending on your instance's `CPU credit specification`:

* if set to `Standard` (T2 default) your CPU slows down drastically (and you won't be able to play anymore).
* if set to `Unlimited` you will have to pay for extra credits spent. (T2.micro: around 0,05ct / hour).

There are of course formulas which let you calculate how quickly CPU credits are spent and how they get back to the maximum possible amount (you'll find them [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-credits-baseline-concepts.html)). Our T2.micro instance used about 100 credits for about 5 hours of game time. After about 17 hours our credit amount was back to the 144 maximum. 

So we get about 6 hours of 'free' gaming time every day out of our T2.micro server. If you plan to spend more time playing either go for a server with more CPU power or pay for the extra credits used.

We decided on setting the `CPU credit specification` to unlimited as we do not want to stop playing in mid game just to save a few cents. To do this 
* open the EC2 Dashboard
* click on  "Instances"
* select your server's instance
* click on "Actions => Instance settings => Change credit specification"
* activate "Enable unlimited mode"

Note: we found out that the server's `tickrate` is an important factor for the CPU load. For roughly the same game time as in the example above a `tickrate` of 128 used all the available 144 credits and suddenly the server basically stopped working (this is how we found out about CPUcredits :)). So now we have set the `tickrate` to 64 without noticing any relevant side effects. 

## Current costs for running the AWS Server ##

Currently we are still in the free 12 month try out period for our server, so we are not paying anything for the server itself. Additional costs occur
- for the additional 30GB disc size (around 3€ / month)
- for using more then 30GB of band width / data transfer (around 1€ / month)

We spend a few cents for the Elastic IP before we found out this only costs money as long as the server is NOT running.

We changed the CPU credit specification from standard to unlimited but so far did not have to pay for any extra CPU credits (but then our current profile is to play once a week for about 4-5 hours).
