---
layout: post
title: On why QA systems should be monitored
date: '2011-06-15T23:18:43-07:00'
tags:
- devops
- monitoring
- QA
tumblr_url: http://www.buildscientist.com/post/6514582536/on-why-qa-systems-should-be-monitored
---
### Generally when it comes to monitoring systems most shops typically focus on monitoring production class/grade boxes. 

When it comes to QA systems on the other hand that’s something rarely done - or at least something I have seen rarely done.
There are a number of compelling reasons why QA systems should  be monitored. It may seem a bit overkill but depending on the complexity of your application/system architecture it’s something that will provide peace of mind for your development, QA and operations teams. 

Lets create a fictitious social networking company called SunnyLOL. SunnyLOL has a fairly simple web application that lets people all over the world know what people are doing when it's 

- Sunny 
- They are LOLng

---
The underlying frameworks, hardware and technology stack are inconsequential for this conversation. They could be running a web server farm on a cluster of Amiga 500’s for all I care. The people, work and time involved in the SDLC (specifically the QA portion of the SDLC) on the other hand are of importance here. 

How many times have any of you walked into the office one morning and overheard a conversation like this? 

> Hey is xyz service running?

> I dunno - it should be.

> Ok well all my test harnesses are failing - they were working last night 

> Umm well maybe IT patched the QA boxes and …

> No - that’s not it. *sigh* I’m gonna go talk to ops/devs/whomever


What’s wrong with this conversation? Well besides the unsupportive/finger pointing at work colleagues this entire conversation/frustration could have been avoided. How? By setting up identical or similar levels of application/system level monitoring in QA. 

Now before you say “ugh that’s too much work - and besides real QA systems should be setup/torn down for every test suite” - I would say yes that’s right but it doesn’t mean one shouldn’t factor in monitoring on QA systems. Just because it’s not in production doesn’t mean it isn’t important. For a QA engineer the QA system/environment they’re working on might as well be production.

Here’s another good reason why QA systems should be monitored. Lets say your trusty build engineer puts out a new build and because you follow the best practice of code/build promotion it eventually ends up into a QA environment and they discover that this new build has borked the application monitors in QA. The QA engineer discovers this while going through their test cycle (or possibly gets notified by incessant email/IM notifications), makes note of the issue and notifies operations and flags the project team (without blaming/pointing fingers/cussing out development).

*Happy happy joy joy* (or something to that extent) - not only have you made operations life easier but you’ve also caught a potential issue earlier on in the SDLC. 

That’s devops - that’s professionalism - that’s how *real* engineers work. 