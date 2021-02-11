---
layout: post
title:  "Improving your Sentry issues when using Python's structlog"
date:   2021-02-11 12:22:40 +0000
categories: engineering python learning
---

This week I had to investigate a better approach to our logs. The problem was that we have a bunch of microservices running freely and wildly, in other words: zero observability and monitoring. When I first introduced Sentry to monitor exceptions, I saw multiple issues with a single event that represented the same one.

Going a little further on the investigation, I realized that our logs were being sent to Sentry as a string instead of JSON. Because of that, Sentry was not able to get a traceback, and most of the issues were truncated.

I investigated many alternatives. The first one was to start using `exc_info=True` in all logs in which `level >= ERROR`.
The stack trace also got converted to string on the `message` payload of Sentry. After that, I did a small test raising an `Exception` out of nowhere to test if Sentry would create an issue and show the stack trace. It worked very well. Because of that, I was able to identify that we had a possible on our logging library.

Our logging configuration was pretty simple, but I realized that we were using [Structlog](https://www.structlog.org/en/stable/) instead of Python's default log configuration. Structlog is a library that adds structure to your log, making it easier to add searchable information.

Somewhere in the middle of the whole process, the log gets converted to a string. It is then sent to Sentry, losing the JSON format and its properties. To fix this problem, I found another library called [structlog-sentry](https://github.com/kiwicom/structlog-sentry). This library adds a new processor to structlog. It tells Sentry to ignore the logger and captures the events manually.

By adding this new processor, Sentry was able to show the stack trace and the right exception message.