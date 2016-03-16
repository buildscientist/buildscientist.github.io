---
layout: post
title: Implementing Database Change Management
date: '2011-12-21T21:48:00-08:00'
tags: []
tumblr_url: http://www.buildscientist.com/post/14604558920/implementing-database-change-management
---
### Continuous integration. I’m practically in *love* with the concept. 

I’ll never forget the first time I read Martin Fowler’s infamous [article](http://www.martinfowler.com/articles/continuousIntegration.html) on the topic. I was dazzled - I was amazed - it all made *perfect* sense. 

The first opportunity I got I wanted to implement end to end CI. As I started to dig into this wonderful concept of automation I hit a brick wall when it came to continuously rolling out DB changes. 

This is one of many perspectives I have on the topic …

A key component to having true continuous integration of your builds is implementing database change management (DBCM). Loosely defined it is: 

> The process by which changes made to a database are tracked and version controlled over a period of time.

Most development shops tend to overlook DBCM for a number of reasons:

> DBCM is challenging to setup.

True but once you put the effort in setting up it’s well worth the time and effort taken to do so. Plus if you’re committed to having true CI you’ll do it. There is no ‘one size fits all’ solution when it comes to DBCM. Isn’t that generally the case for anything development related? Your code is unique (well you like to think it is :) so why would you expect your DBCM solution to be identical to anyone else? 

> Developers are accustomed to making changes directly to a DB using a SQL code generator like SQLYog or PHPMyAdmin.

Even if you’re using these tools you should still be able to capture the SQL generated and apply DBCM to it. 
We have DBA's who perform DB sorcery/wizardry to roll out DB changes into various environments. 
You say sorcery/wizardry and I say painful DB diffs. You also lose an important aspect of DBCM - the order in which DB changes are applied. 

Excuses and joking aside DBCM is a very attainable goal. Let’s discuss a number of items that need to kept in mind when implementing DBCM in your development organization:

> Establish a set of DBCM guidelines. 

This is your manifesto. These are the guidelines your developers will be referencing on a constant basis to remind themselves how to do DBCM properly. 

> Understand why you are doing DBCM.

You are not doing this for the sake of process. You are not doing this to be a pain in the rear to developers. This is being done because changes made to a database need to be tracked and version controlled. 

> Make the development of DBCM in your organization a cross functional task.

I recommend involving at least 1 representative from development, DBA and your release management teams. 

----

Lets talk about some of the components that make up a solid DBCM process:

> Version control your DB changes. 

Even if you are using a tool to generate SQL find a way to capture that code and version control it. 

> Decide on the submission format of your changes.

Are you going to drop all of your changes into 1 file or chunk them into smaller patch files? Each technique has it’s pros and cons. Putting all your DB changes into one file makes it a lot easier to apply an end to end change across a DB.
On the other hand chunking your changes into individual delta files allows you to keep track and apply individual changes. It also allows you rollback individual changes - provided separate rollbacks are provided. 

> Design/code review your changes.

You code review your application level changes so why shouldn’t your DB level changes undergo a similar process?

> Select an automated DB deployment tool that allows you to keep track of changes applied across your database environments.

There  are DB deployment tools (DB Deploy, LiquiBase, Flyway) that implement change logs via DB tables to keep  track changes of applied.

> When continuously integrating your builds tie your DB changes in with your application level changes - if applicable.

Meaning if an application build requires a database level change - roll out of the build and the DB change should be atomic. 

> Include a rollback/undo change along with every change that is applied. 

If you end up putting all your changes into one file making a rollback for 1 change is going to be a lot more challenging. This is why I am more in support of having individual patch/delta files for each change.

All six of the items listed above deserve their own articles. In my next few posts I’ll be discussing how version control, continuous integration and automated DB deployment all tie into DBCM. 