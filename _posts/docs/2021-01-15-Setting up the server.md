---
layout: post
category: documentation
title: "Setting up the server"
date: 2021-01-15
---



We decided on a server hosted by Amazon Web Services (AWS).

Amazon for a 12 month trial period offers a free server with certain specifications. These are (as the time of writing) (nearly) enough for our purposes. We only had to add additional disk space, as the 30 GB were not sufficient for all the game files. Currently we have upgraded to 60 GB which cost about 3 US$ a month.

## Basic installation ##

This [link] (https://www.reddit.com/r/GlobalOffensive/comments/hro0ct/setting_up_your_own_dedicated_cs_go_community/) takes you to "a step by step guide on How to set up your own dedicated CS: GO community server using AWS for free. Don't worry. It's a noob's guide." 

It explains in detail
* the required Pre-requisites for setting up the server 
* creating a (Ubuntu) linux server using Amazon’s EC2 services 
* logging into that server using ssh
* installing steam and Counter Strike : Global Operations
* creating a game token for your dedicated server
* adding the game to the servers auto start procedure

Attention: before the steam installation we had to execute the following line

    sudo apt install libsdl2-2.0-0:i386

to add some missing packages.

## Increasing disk space ##
The above guide will give you an installation which in our case used nearly 29 GB of the server’s initially available 30 GB disk space. This is not enough to install updates or additional maps– and may in the near future not be enough to even install the game. For this reason you **will** have to increase the disk space available. Increasing it to 50 GB should be sufficient. 
Note: Increasing the disk space will cost you about 0,1 US$ per GB a month. For exact prizing look for Amazon Elastic Block Store (EBS) costs.
To increase your server’s disk space you have to 
* add more disk space to the instance
* assign that disk space to the hard drive’s configuration.
To add disk space to your instance
* In AWS Services select EC2 (dashboard), click on your instance and you should see the details panel displayed.
* In the details panel click on “Storage” and there on the (single) volume of your instance
* On the next page click on “Actions” and select “Modify Volume”
* A window pops up where you can change the size of the volume. Beware that sizes above 30GBs will cost you a monthly fee.
* After clicking on “Modify” the new space will be assigned to your instance.
