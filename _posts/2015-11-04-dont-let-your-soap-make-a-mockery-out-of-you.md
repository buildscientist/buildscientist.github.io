---
layout: post
title: Don't let your SOAP make a mockery out of you!
date: '2015-11-04T19:00:00-08:00'
tags: []
---

### Recently as part of my day job I was tasked with helping test a small SOAP based web service that a colleague of mine had written. 

You might be thinking - what sort of *personal* **hell** was the Buildscientist put through in having to deal with a SOAP (small or not) web service. Allow me to digress from the main topic of this post. 

Everyone should be writing RESTful webservices right? Well no - SOAP based webservices still have their use cases. Other folks in the Interwebs have discussed the [merits](http://stackoverflow.com/questions/19884295/soap-vs-rest-differences) of using SOAP or REST based webservices. 

There are a number of tools both commercial and open source that are out there that are well suited for this task - specificially SOAPUI.

Don’t get me wrong - the fine folks over at SmartBear have made a product that reduces the complexity of testing both RESTful and SOAP based web services - but for the lean minimalist in me the tool is a bit too heavy weight. 

Besides I'm a developer - I want to be able to look at clean code not a massive XML document with serialized representations of SOAP endpoints and stubs to said endpoints.

What’s a lean minimalist dev like myself to do? Use [WireMock](http://wiremock.org) of course.

Wiremock is a Java based library used for stubbing and mocking of web services. I love the API’s simplicity (builder pattern for the win) and its integration with JUnit.

It’s also a great prototyping tool - although if I really need to prototype a RESTful web service in a jiffy I’d rather stick with [Flask](http://flask.pocoo.org/). That said if you’re stuck in a Java only environment - Wiremock is a solid prototyping tool.

One of WireMock’s most powerful features is the ability to validate either JSON or XML based HTTP requests. In my case I had a very basic SOAP web service with one endpoint and a single SOAP operation that returned an XML document. 

Creating a basic WireMock service that will return "Hello World" is a easy as follows: 

{% gist 484751422cbf1fc79b71 helloworld.java %}

Notice how the call to stubFor() on line 3 uses the builder pattern to complete creation of the web service. WireMock exposes a boat load of HTTP specific handlers that will allow to craft what type of HTTP response to return as well as the type of response to expect. 

Since I am unable to share my employers code I decided to create a demo of WireMock that mocked a public SOAP webservice available on W3Schools. The [TempConvert](http://www.w3schools.com/webservices/tempconvert.asmx) SOAP service does two basic things: 

- Converts Celcius to Farenheit
- Converts Farenheit to Celcius

-----

I decided to use WireMock's handy JUnit testrule that takes care of setup & teardown of my WireMock instance. Creation of the WireMock service and stub for the TempConvert endpoint is highlighted as follows: 

{% gist 484751422cbf1fc79b71 tempconvert.java %}

One of WireMock's powerful features is that it provides the capabilty of verifying XML via XPath. If you're not familiar with XPath - it's basically a query language for XML. 

Given that my SOAP request and response bodies are all in XML - WireMock gives me full programmatic ability to generate my own SOAP envelopes and expose them directly in my mocked webservice. 

For purposes of this demo I've created two XML files - one for the SOAP request and another for the SOAP response. 

To access the WireMock endpoint - you have the option of using your own SOAP client library or using an HTTP client like curl and crafting your own SOAP requests. I opted to use a lightweight SOAP client called [soap-ws](https://github.com/reficio/soap-ws) that is touted to "handle SOAP on a purely XML level".

WireMock also gives you the ability to verify incoming requests from your SOAP client. Using a tool like soap-ws you have end to end testing wholly encapsulated in a JUnit test. Verification of incoming requests is as follows: 

{% gist 484751422cbf1fc79b71 verifyrequest.java %}

In short if you're looking for a lightweight and incredibly powerful tool/library to test your SOAP webservices I highly recommend using WireMock. 

For more detailed code examples - feel free to check out the full implementation of my WireMock service mocking the TempConvert SOAP service on [Github](https://github.com/buildscientist/wiremock-demo).