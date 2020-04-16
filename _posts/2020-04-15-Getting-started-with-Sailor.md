---
layout: post
title: "Getting started with sailor"
date: 2020-04-15 22:48:00 +0200
author: hidalgopl
categories: Sailor Tutorials
tags: Sailor Tutorials SecureAPI
---

In this post we will learn how to run security checks for you website using sailor & SecureAPI.


Our first step on security journey will be downloading sailor, which is command line tool, provided by SecureAPI to run security checks against your website backend API.
Once you have it downloaded, verify if it's installed correctly by running 
`sailor version`

You should see similar output:
```bash
$ sailor version
Sailor v0.0.3
```
Great! Looks like everything is okay.
Let's explore sailor features.
Now, type simply `sailor`
Here's what you should see:
```bash
$ sailor
Usage:
  sailor [flags]
  sailor [command]

Available Commands:
  config      Print the loaded config
  feedback    Asks you 5 simple questions and send your feedback to us.
  help        Help about any command
  run         Runs security test suite!
  version     Print the version number

Flags:
      --config string   config file (default is config.yaml)
  -h, --help            help for sailor

Use "sailor [command] --help" for more information about a command.

```

We would be mostly interested in `run` command, however there are few more.
`config` is mostly to debug if your configuration file is readed as expected.
`feedback` is a tool that will ask you 5 short questions about our product. We really appreciate your feedback and it should be super quick!
You can just type answers right into the console and hit enter, and that's it. No logging in, no google forms, as we know developers are busy (at least we are ;)

Getting back to running test, we gonna use `run` command. Before we do it, we need to create our `config.yaml` file in the directory from which we gonna run sailor.
`config.yaml` consists of 3 key-value pairs:
```yaml
username: "hidalgopl"  # place your username here
accessKey: "your-long-access-key"  # you can get your access key from User profile section on secureapi.dev
url: "https://api.for.my.backend.com"  # here you should put URL to the API you want to test
```
and that's mostly it!

Now, let's get down to business.
Type `sailor run` and starting testing your API.
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

as you see, some security checks failed. Now you can click on the link at the bottom at it should open tests tab and highlight tests that failed.
As you can see, there is a column named solution. Once you click on it, you'll be taken to Solutions tab where you can learn why this check failed and what can you do to harden your API security to let them pass
_And that's all folks!_