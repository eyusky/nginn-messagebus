
---

```




```

---




### Migrated to GitHub https://github.com/lafar6502/nginn-messagebus ###


Project moved to https://github.com/lafar6502/nginn-messagebus because Google Code is shutting down.


```




```



















---

```




```

---




## What is NGinn.MessageBus ##

NGinn Message Bus is a simple and lightweight ESB for .Net supporting publish-subscribe message distribution. It uses SQL server as message queuing mechanism - each message queue is stored in a separate table.

NGinn Message Bus is a part of my BPM/Orchestration engine called NGinn BPM and serves as a message-oriented communication bus. NGinn Message Bus is functionally similar to other .Net message bus implementations (NServiceBus, MassTransit, Rhino Service Bus) but it's the only service bus based on SQL Server database.

Go to [project site](http://lafar6502.github.io/nginn-messagebus-pages/) for more details.

Update (sep 10, 2012): NGinn.MessageBus can now use MongoDB database instead of SQL server (optional feature).

## Installation ##
The easiest method of incorporating nginn-messagebus into your project is to use a [NuGet](http://www.nuget.org) package. The latest binary version is always available at
http://www.nuget.org/packages/nginn-messagebus/

There's no tutorial to follow, but the source code tree contains a number of example projects that show how to configure and use the message bus. It's not difficult.

## Why SQL database ##

  * because it's easier to admin a database than a set of queues
  * because your data and messages can be kept together and backed-up together
  * because of easier and more efficient transactional processing
  * and because of quite good performance

## Main Features ##

NGinn Message Bus implements publish-subscribe message distribution mechanism and handles transactional & durable messages only. It offers a good performance in transactional processing (is able to process about 1000 messages/second on a cheap laptop) and there's very little performance penalty when using distributed transactions - actually it outperforms MSMQ in typical scenarios involving DTC. NGinn Message Bus has also:
  * support for scheduled messages (messages delivered at specified moment in future)
  * robust retrying strategy for failing messages
  * message TTL (time to live) support
  * sequences - guaranteed order of delivery for a group of messages (by default the order is not guaranteed)
  * http monitoring and management
  * sql and http message forwarding

NGinn project is a work in progress and so is NGinn Message Bus. However, it's already being used in production without any problems.

See [GettingStarted](GettingStarted.md) page if you want to learn the basics.

## Disclaimer ##
Feel free to contact me in case of any problems with nginn-messagebus or if you have any questions. I realize this project is not documented enough and there are no good quality tutorials but don't be discouraged by that - it's working in production without too many problems. Some working examples are provided so use them as a tutorial.

## Support ##
https://groups.google.com/forum/#!forum/nginn-messagebus

## Recent changes ##
  * 13 Feb, 2013 - repository changed to Git, old svn repo available here:http://nginn-messagebus.googlecode.com/svn/
  * jan 2013 - MongoDB support works, production testing needed
  * sep 10, 2012 - Working on MongoDB transport
  * oct 06, 2011 - Added Saga implementation: [Sagas](Sagas.md)
  * sep 19, 2011 - Added simple json-based rpc mechanism

## When to choose nginn-messagebus ##
  * If you are already using SQL Server and dont want to introduce new technologies to your application
  * If you're not familiar with MSMQ or other message queuing mechanisms but want to have async and durable messaging
  * If your db admins start screaming at you when you mention MSMQ or some other exotic technology they never heard about
  * If you want to keep all the data and messages in a single database
  * When transactions in your application include data operations and sending/receiving of messages

## When to look for something else ##
  * If you need to process thousands or tens of thousands of messages every second
  * If you don't need durable and transactional queues
  * If you're not using SQL Server
  * If you're already using some other messaging software

## Source code access ##
SVN checkout:
[https://nginn-messagebus.googlecode.com/svn/trunk](https://nginn-messagebus.googlecode.com/svn/trunk)


## Related projects ##
[NGinn.BPM](http://nginn.codeplex.com) - Workflow/BPM engine for .Net

[nginn-exchange for MS Exchange integration](http://code.google.com/p/nginn-exchange)