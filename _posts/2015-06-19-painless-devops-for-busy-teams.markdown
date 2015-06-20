---
layout: post
title:  "Painless Devops for Busy Teams"
date:   2015-06-14 20:42:52
categories: devops aws
---
#Or How I Learned to Stop Worrying and Love AWS Opsworks

###Plan For Success and Failure
When we started work on CarLingo our operational goals were to plan for success and failure from the start. Planning for success meant ensuring the product will be easy to scale and failure planning meant that we had to have monitoring and backup/recovery capabilities that enabled us to move fast and break things.

Basically, we wanted our production and staging environments to be "big boy" software from day one, which meant;  

* load balancers and failover for our web tier
- monitoring/alerting on all resources
- log aggregation with visibility
- push button hot deployment
- static assets served via CDN
- caching of all external API calls
- 100% cloud based

Many cloud providers would have given us the benefits of easy scaling, zero capex, low opex, and system flexibility. We picked AWS over other cloud hosting solutions due to the number of [capabilities AWS provides](http://aws.amazon.com/solutions/) in one place.

###Why OpsWorks
[AWS OpsWorks](http://aws.amazon.com/opsworks/) is an application management service that makes it easy to deploy and operate applications of all shapes and sizes.

You define stacks, layers, and applications.
A Stack is a logical group of Layers/Applications that serves a common purpose. We have development, staging, and production stacks. Inside each stack are layers and applications. Layers are blueprints for AWS resources. The Layer defines each resources settings and configurations, default layers exist so you can quickly create caches, databases, and web app layers. Applications represent code in a repository you wish to install on a resource. All of this is easy to control and visualize via a well designed dashboard.

AWS OpsWorks is designed to run [Chef](https://www.chef.io/chef/) cookbooks and comes with a [large library of cookbooks](https://github.com/opscode-cookbooks/aws) powering the default Layers. Chef allows you to define your infustructure as code. I have used [Puppet](https://puppetlabs.com/), [Ansible](http://www.ansible.com/home), and [Fabric](http://www.fabfile.org/) in the past and found Chef easy to learn. It has a strong and helpful [community](https://supermarket.chef.io/) and extending a default OpsWorks layer to meet you specific needs is easy and quick.

OpsWorks integrates easily with other AWS resources; Elastic Load Balancers, RDS, ElastiCache, and CloudWatch monitoring and alerting are all wired up easily. For example, If you define an RDS layer in your OpsWorks Stack and then select it in your Rails Application the correct ```database.yml``` config file for RDS will be dropped into your web app on deployment.

###What We Had to Customize
Very little. Our kit is almost completely vanilla OpsWorks. We created a few custom recipes that configure a custom nginx configuration, install some ip geo software and setup log aggregation. A few very short Chef recipes is the extent of our devops work.

OpsWorks comes with basic monitoring and visibility and setting up additional monitoring with alerting was trivial via AWS CloudWatch.

###Would I Recommend OpsWorks to a Friend
You bet 9/10! You can count me as "Loyal Enthusisats" on the [Net Promoter](https://en.wikipedia.org/wiki/Net_Promoter) scoring scale. Our team is busy and AWS OpsWorks allows us to focus almost 100% of our effort on features and not configuration. I recommend you try the [walkthrough](http://aws.amazon.com/opsworks/getting-started/) today!


Mark Conlin  
CTO & Co-founder  
CarLingo  
