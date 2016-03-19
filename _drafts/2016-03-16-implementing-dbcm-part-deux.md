---
layout: post
title: Implementing DBCM Part II
date: 
tags: [dbcm,flyway]
---

### Wow so it's been almost 5 years since I wrote my last post on implementing DBCM. 

A lot has happened since then but many of the concepts and practices outlined in that [post]({% post_url 2011-12-21-implementing-database-change-management %}) are still very much applicable. 

In case you forgot or are looking for the TLDR of that post - here's a high level overview. 

- DBCM is the process by which changes made to a database are tracked and version controlled over a period of time.
- Establish a set of DBCM guidelines. 
- Understand why you are doing DBCM.
- Make the development of DBCM in your organization a cross functional task.

---
In my previous post on DBCM I recommended the use of an automated DB deployment tool. I have found that [Flyway](https://flywaydb.org) contains a feature set that gels well with agile development teams. I've used Flyway since v1.x and it's come a long way. It started off supporting a handful of well known relational databases (MySQL,PostgreSQL, Oracle) and now supports well over a dozen databases. 

Flyway is a Java based tool that provides a Java API, a Maven goal and a standalone command line mechanism of deploying DB changes. While its features are far too great for me to go into detail in one post - I'll focus on its basics by integrating it with a Jenkins based self service job. 

One of the advantages of integrating it with Jenkins is you can easily expose a handy web based form driven tool to your engineers that can be used across development, QA and even production environments. As an added bonus - by using Jenkins our self-service tool is also exposed via REST allowing for batch deployments across multiple environments. 

In this post I’ll be discussing the following:

1. How to setup Flyway using Maven.
2. Executing Flyway with Maven.
3. Integrating Flyway with Jenkins.

---

##### Technical Requirements: 
- Java 7 or higher
- Maven 3.x
- Relational DB such as MySQL or PostgreSQL - We'll be using MySQL 5.7
- JDBC drivers for your relational DB
- Delta/Patch files to apply against a relational database. 
- Latest version of Jenkins - 1.635 as of this post
- [Jenkins Pipeline plugin](https://wiki.jenkins-ci.org/display/JENKINS/Pipeline+Plugin) 
- [Conditional Buildstep Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Conditional%20BuildStep%20Plugin)
- Latest version of Flyway - 4.x as of this post

---

##### Initial Setup: 

After installing/obtaining all of the required dependencies listed follow the handy [first steps guide](https://flywaydb.org/getstarted/firststeps/maven) provided by Flyway to generate a POM (Maven build script) using the Maven archetype plugin. 

Depending on the parameters passed to the mvn archetype plugin you should end up with a directory structure similar to the following:

```
├── pom.xml
└── src
    ├── main
    │   └── java
    │       └── com
    │           └── buildscientist
    │               └── flyway
    │                   └── App.java
    └── test
        └── java
            └── com
                └── buildscientist
                    └── flyway
                        └── AppTest.java

```
Open up the POM in your favorite text editor (*cough* vi *cough*) and add the following **```<dependency>```** block (assuming you're using MySQL)

``` xml
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.38</version>
    </dependency>  
```

Next we need to configure the POM to have Maven process delta/patch files out of the same directory the POM is in. The alternative is having the SQL files processed out of the default resources directory - e.g. **src/main/resources/db/migration** which isn't neccessary unless you're packaging your patch files along with application code. You'll need to override where Flyway looks for source files by adding this block of XML within the **```<configuration>```** definition of the Flyway plugin. 

```xml 
    <locations>
        <location>filesystem:${basedir}</location>      
    </locations>
```
If you do end up doing this you'll no longer need your "src" directory - so feel free to [86](<https://en.wikipedia.org/wiki/86_%28term%29>) it ... safely. Remember folks - **safety** first - one should never do a **rm -rf** ...

---

##### Running Flyway:
Flyway is fairly trivial to run. Plugin goals are described [here](https://flywaydb.org/documentation/maven/) on Flyway's website. The key item to understand is the different between a running a migration and baselining a database. 

A baseline is run as **mvn flyway:baseline**. 

In short a baseline is what you want to run against an existing database. The flyway:baseline goal adds in a schema metadata table to your existing database that keeps track of all migrations run against your database. Your baseline SQL patch should create all the original DDL required to create the database so that new (copies of the existing db) databases instantiated are able to obtain all the changes.

**Note**: You may be tempted to *sneak* in some changes to the baseline to include other changes that occurred after the intial database was created but **DON'T** do it. One of the key points of DBCM is to track changes made to your DB over a period of time. If you sneak in a change to the baseline - how will other engineers understand when/why that change was made? 

Subsequent runs against an already running database would utilize the migration goal. A migration is run as  **mvn flyway:migrate** - this would apply all patch files created **after** the baseline. This Flyyway Maven goal would also be run against any new (empty) databases that haven't instantiated yet. In this case all patch files including the baseline would be executed against the database by Flyway.

**Note**: Your patch filenames need to be in a specific format. Flyway does allow you to change the default format but I recommend against doing that. The default format is ```V1__baseline.sql``` where the generalized form is ```V#__description.sql``` - note the double underscore between the version and the description of the change.

For more details on other Flyway Maven plugin goals - see the Flyway [documentation](https://flywaydb.org/documentation/).

---

##### Integrating Flyway with Jenkins:

As stated in the Technical Requirements portion of this post - you'll need to have the latest version of Jenkins installed as well as the Jenkins Pipeline plugin and the Conditional Buildstep Plugin. Exposing Flyway using Jenkins might seem a bit strange (read: *hacky*) at first but if you're practicing continuous integration you'll want to integrate Flyway with your builds. Either way Jenkins provides us with a relatively trivial way of setting up a web based form to apply DBCM across a database. 

As an added bonus you get the following for "free": 

* RESTful interface
* Authentication 
* Logging
* Access Control