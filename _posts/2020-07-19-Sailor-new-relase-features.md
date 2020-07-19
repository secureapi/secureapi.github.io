---
layout: post
title: "Sailor v0.1.0 - what's new?"
date: 2020-07-19 16:54:00 +0200
author: hidalgopl
categories: Sailor Announcements
tags: Sailor Release Announcements SecureAPI
---

I'm more than happy to announce that I just released sailor in version 0.1.0. This is another small step forward.

# Historical context
Inspired by [Facepalm Lesson One: The Delayed Aha Moment](https://smallstep.com/blog/delayed-aha/) I started analysing how long it takes to get value from using sailor and, obviously, SecureAPI.
Previous flow for new user was fairly easy: You're signing up with Github, then you copy your sailor access key, next you needed to create `.secureapi.yml` config by hand, paste username and key there as well as URL you wanted to test.
After this, you are all set to run your first security test suite. Sailor used to produce following output:
```bash
$ sailor run
INFO[0000] Authenticated for hidalgopl
INFO[0004] [bqbnr9f69kffvend587g] -> SEC0009 : result: passed 
INFO[0004] [bqbnr9f69kffvend587g] -> SEC0001 : result: passed 
INFO[0004] [bqbnr9f69kffvend587g] -> SEC0002 : result: failed 
INFO[0004] [bqbnr9f69kffvend587g] -> SEC0008 : result: failed 
INFO[0004] [bqbnr9f69kffvend587g] -> SEC0007 : result: passed 
INFO[0004] [bqbnr9f69kffvend587g] -> SEC0006 : result: passed 
INFO[0004] [bqbnr9f69kffvend587g] -> SEC0005 : result: passed 
INFO[0004] [bqbnr9f69kffvend587g] -> SEC0004 : result: failed 
INFO[0004] [bqbnr9f69kffvend587g] -> SEC0003 : result: failed 
INFO[0004] all tasks executed successfully. Link to your test suite: https://staging.secureapi.dev/tests?suite_id=bqbnr9f69kffvend587g
```

To learn what was actually wrong, you needed to open the link in browser and read though test suite summary and follow up to the Solution tab.

# Motivation behind new features

While this flow was OK, I was mainly worried about two moments: first is creating `.securapi.yml` config by hand. It's error prone, as you need to avoid any typos in config keys etc.
By following principles that we have in SecureAPI, everything boring and repetitive should be automated.
So, we're introducing `sailor init-config` command!
All it does it creates config template. This should solve potential typo-related errors and speed up new user onborading. 
Just check it out:

```bash
➜✗ sailor init-config
Config template created: /home/bojan/code/secureapi/sailor/.secureapi.yml
➜✗ cat .secureapi.yml 
username: your SecureAPI username
accessKey: your SecureAPI access key
url: https://api.you.want.to.test.com
```
and that's it. Simple and convenient.

Next part of standard flow what I didn't like fully was need to open web app anytime when I wanted to check what is wrong with my API.
Even though I like our web app for it's simplicity, it slows down whole process. 
That's why I added `sailor explain <TEST-CODE>` command. It allows you to learn what given security test is about and how you should probably fixed.
Just take a look:
```bash
➜✗ sailor explain SEC0001 SEC0004
---------------------------------------------------------------------------------------------
SEC0001: X-Content-Type-Options: no-sniff
The server should send an X-Content-Type-Options: nosniff 
to make sure the browser does not try to detect a different Content-Type 
than what is actually sent (as this can lead to XSS)
Learn more: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options

---------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------
SEC0004: Content-Security-Policy: policy
Content Security Policy (CSP) is an added layer of security
that helps to detect and mitigate certain types of attacks, including Cross Site Scripting (XSS)
and data injection attacks. These attacks are used for everything
from data theft to site defacement to distribution of malware.
Learn more: https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP

---------------------------------------------------------------------------------------------

```
It also gives you links to additional resources if you want to learn more. That's pretty useful also if you run your security tests as part of your continuous integration (which we highly recommend) and after checking the results, you want a ready solution for failed tests quickly.

But wait, there's more!

We also decided to print explanations for failed tests in `sailor run` *by default*.
It means that you no longer need to open our web app to learn how to solve security issues in your API - answer will be in console immediately!

Check it out:
```bash
➜  sailor git:(master) ✗ ./bin/sailor run --config=k3s-local.yaml
INFO[0000] Authenticated for admin                      
INFO[0000] Nats url: nats://otterly-secure:46aa9a8bbb3c474302bb@localhost:4222 
INFO[0000] [bsa5nfl5ictvmai1178g] -> SEC0008 : result: passed 
INFO[0000] [bsa5nfl5ictvmai1178g] -> SEC0001 : result: failed 
INFO[0000] [bsa5nfl5ictvmai1178g] -> SEC0009 : result: failed 
INFO[0000] [bsa5nfl5ictvmai1178g] -> SEC0004 : result: failed 
INFO[0000] [bsa5nfl5ictvmai1178g] -> SEC0005 : result: passed 
INFO[0000] [bsa5nfl5ictvmai1178g] -> SEC0007 : result: failed 
INFO[0000] [bsa5nfl5ictvmai1178g] -> SEC0003 : result: failed 
INFO[0000] [bsa5nfl5ictvmai1178g] -> SEC0002 : result: failed 
INFO[0000] [bsa5nfl5ictvmai1178g] -> SEC0006 : result: passed 
INFO[0000] all tasks executed successfully. Link to your test suite: http://localhost:3000?suite-id=bsa5nfl5ictvmai1178g 
---------------------------------------------------------------------------------------------
SEC0001: X-Content-Type-Options: no-sniff
The server should send an X-Content-Type-Options: nosniff 
to make sure the browser does not try to detect a different Content-Type 
than what is actually sent (as this can lead to XSS)
Learn more: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options

---------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------
SEC0009: Set Cache-Control or Expires header
The Expires header contains the date/time after which the response is considered stale.
If there is a Cache-Control header with the max-age or s-maxage directive in the response,
the Expires header is ignored.
Learn more: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Expires

---------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------
SEC0004: Content-Security-Policy: policy
Content Security Policy (CSP) is an added layer of security
that helps to detect and mitigate certain types of attacks, including Cross Site Scripting (XSS)
and data injection attacks. These attacks are used for everything
from data theft to site defacement to distribution of malware.
Learn more: https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP

---------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------
SEC0007: Strict-Transport-Security: max-age=(age in seconds); (other options)
This header lets a web site tell browsers that it should only be accessed using HTTPS, instead of using HTTP.
Learn more: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security

---------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------
SEC0003: X-XSS-Protection: 1; mode=block
This header enables the Cross-site scripting (XSS) filter in your browser. 1; mode=block Filter enabled.
Rather than sanitize the page, when a XSS attack is detected,
the browser will prevent rendering of the page.
Learn more: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-XSS-Protection
---------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------
SEC0002: X-Frame-Options: deny or sameorigin
The server should send the X-Frame-Options security header with deny or sameorigin value,
to protect against drag'n drop clickjacking attacks in older browsers.
Sites can use this to avoid clickjacking attacks, by ensuring that their content 
is not embedded into other sites.
Learn more: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options

---------------------------------------------------------------------------------------------

```
 
# TL;DR
  - sailor v0.1.0 introduces two new commands: `sailor init-config` and `sailor explain <TEST-CODE>`
  - `sailor init-config` generates `.secureapi.yml` config template
  - `sailor explain SEC0004` prints description of security issue with solution and resources to learn more
  - From now on, `sailor run` will print security issues descriptions and solution to console, to speed up feedback loop
  - motivation for all those features was mainly speeding up getting value from sailor and easing new user onboarding
 
_And that's it folks!_ Stay secure, stay safe.
