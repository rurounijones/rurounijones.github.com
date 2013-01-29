---
layout: post
title: "How to ask for help on IRC"
date: 2009-03-17 01:24
comments: false
categories: [general]
description: How to ask for help properly on IRC so that you get responses 
             faster. Also relevant to forums and mailing lists
keywords: help irc
published: true
---

Greetings. If you have arrived at this page via your own volition and intent
then be aware that this is an abridged, slightly more modern, IRC specific 
version of Eric S. Raymond's "[How to ask questions the smart way](http://www.catb.org/~esr/faqs/smart-questions.html)"
which is excellent, but lengthy, reading.

If, on the other hand, you have been directed to this page by someone else and
quickly want to find out what this is about, Read on.

<!--more-->

## How to ask for help properly

In short:

* Don't ask to ask
* Give the helpers some details
* Ask suitable questions
* Be polite
* Wait

For the purposes of this article I will introduce "Terry" and "Gonad" 
(Of "[Zero punctuation](http://www.escapistmagazine.com/videos/view/zero-punctuation/130-Alone-in-the-Dark)" fame). 
Terry shall be our long suffering helper in the "Spiffy" project's IRC channel
and Gonad shall be the person asking for help (badly).

### 1 - Don't ask to ask

This one drives people up the wall and many channels have special bots that will
print out an entire spiel about not asking to ask, so let's get it out of the
way first.

```
[16:00] * Gonad has joined #spiffy-help
[16:00] * Topic is "Welcome to the spiffy help channel. Latest version is 1.5"
[16:00] <Gonad> Is it ok to ask a question about Spiffy?
[16:01] * Terry sobs quietly in the corner
```

Let us look at the obvious first. The entire channel is dedicated towards
helping people with Spiffy, this is usually hinted at in the channel name and 
outright does in the topic. Do you think they have a personal grudge against 
you that is going to stop them answering? (they might possibly later, but right
now it is a blank slate) **of course they can help you**. Just ask the question 
straight out. 

What Gonad should have done was:

```
[16:00] * Gonad has joined #spiffy-help
[16:00] * Topic is "Welcome to the spiffy help channel. Latest version is 1.5"
[16:00] <Gonad> Hi all, I am having an issue with ....
```
Which leads us nicely onto the next point

### 2 - Give the helpers some details.

Let us follow on from the previous point and continue Gonad's sentence.

```
[16:00] * Gonad has joined #spiffy-help
[16:00] * Topic is "Welcome to the spiffy help channel. Latest version is 1.5"
[16:00] <Gonad> Hi all, I am getting an error page with Spiffy, what is going 
                wrong?
[16:03] <Terry> Wait a moment, I just need to psychically connect with your 
                brain so I can figure out what you are talking about. This may
                cause loss of control over your bodily functions.
```

Moral of the story, "Helpers are not psychic". When you post a problem we need
something to go on. Preferably some or all of the following:

* The error page (Or output thereof).
* A list of steps taken which generated this error page and what you are 
  expecting it to do instead of the error.
* The version of the software you are running along with any plugins you might
  have installed. If you think it is pertinent then the OS version and Database
  if used.

A note about including error pages or output. Because these things are 
traditionally huge, pasting them directly into the channel will make you as 
well liked as a clown at a funeral. Instead use a "paste" website like 
"[Pastie](http://pastie.org)", "[Pastebin](http://pastebin.com/)" or 
"[Gist](http://gist.github.com/)" and then paste the URL into the channel.
These sites also offer syntax highlighting which is very useful.

If the problem is complicated, or the steps very detailed, then consider posting
a summary in IRC and more detail in your linked paste.

So once more let's look at what Gonad should have done.

```
[16:00] * Gonad has joined #spiffy-help
[16:00] * Topic is "Welcome to the spiffy help channel. Latest version is 1.5"
[16:00] <Gonad> Hi all, I am getting an error page with a new Spiffy 1.5 
                installation when I try and create a second admin user. 
                Steps and Details here: http://pastie.org/417957
[16:02] <Terry> Gasp, what a well formatted cry for help, I rush, RUSH to your 
                aid.
```

And they all lived happily ever after.

### 3 - Ask suitable questions.

I hang around in some programming channels and every now and then we get a question illustrated by 
the following:

```
[16:00] * Gonad has joined #programming
[16:00] * Topic is "Welcome to the New Hotness #programming channel"
[16:02] <Gonad> Hi guys, how do I build a search engine / forum / fission reactor 
                cooling loop fluid dynamics modelling application.
[16:07] <Terry> By simply asking that question you have betrayed your inherent 
                cluelessness and I shall now ignore you until the heat death 
                of the universe.
```

Let us take a real-world equivalent. Your town has a local club of burly men, 
covered in engine oil, who like messing around with car engines: tuning, fixing
and the like and they meet every night. Now imagine a someone walks into 
their workshop and asks "Hey guys, I want to design and build an engine, can you
quickly tell me how to do that? I have my notepad and everything"

Be thankful that at least online you have a certain amount of anonymity and 
are not vulnerable to immediate physical retribution.

We are a help channel, this means we usually answer technical questions. 
We sometimes answer non-technical questions but don't ask anything that will 
require an entire business/technical specification or 4 year education course
to answer

Further reading on this subject can be read at the 
"[Help Vampire](http://www.slash7.com/pages/vampires)" site (Which is very funny
and better illustrated that this site). Make sure you don't end up as one.

### 4 - Be polite

Common sense dictates that when you put yourself in a position where you are 
relying on the kindness of strangers if behoves you to be polite to those 
strangers.

Remember how much you paid for the support contract on the software? Exactly. 
99.99% of the helpers are not being paid, they are volunteers and generally nice
people doing this because they like the warm fuzzy feeling they get from helping.
Acting like a prat really discourages them from continuing this noble endeavour.

Examples of bad behaviour:

* Being arrogant.
* Discarding/ignoring answers because they aren't quick fixes or require
  using your brain a bit
* Not thinking for yourself and expecting to be spoon fed. (Not Googling your 
  problem before asking is a prime example).

### 5 - Wait

This is the internet, land of many timezones. Therefore questions might not be 
answered immediately. Let us look at Gonad again.

```
[16:00] * Gonad has joined #spiffy-help
[16:00] * Topic is "Welcome to the spiffy help channel. Latest version is 1.5"
[16:00] <Gonad> Hi guys, What is the configuration option for spiffy to enable
                emails?
[16:01] <Gonad> Anyone?
[16:04] <Gonad> Hello?
[16:09] <Gonad> Fine, don't talk to me
[16:10] * Gonad has left #spiffy-help
[16:11] <Terry> Hello guys, back from inventing a cure for cancer, anyone need 
                help?
```

Sometimes answers can take hours/days in a relatively quiet channel, state your
question and wait. Most people will type your nick when replying so make sure 
your IRC client is set to alert you when your nick is typed.

## Conclusion

If you have read this far and actually read everything then congratulations. 
You should now know enough to not be a prat and get ignored whenever you ask a
question in IRC. However this is only the first step; I still recommend reading
Eric S. Raymond's 
"[How to ask questions the smart way](http://www.catb.org/~esr/faqs/smart-questions.html)"
for a more thorough explanation of everything... go on, don't be adequate, go 
and be clued up.

### Real Life Examples.

Fun competition time. Can you sees what rules are being broken with the
following real life examples? (Names changed to protect the... innocent.)

```
#passenger on irc.freenode.net
[21:58] <Gonad> what is wrong with this shit ?
[21:58] <Gonad> *** Exception Errno::EPIPE in Passenger RequestHandler (Broken pipe)
```

```
#kubuntu-offtopic on irc.freenode.net
[03:55] > Gonad has joined this channel (***).
[03:55] <Gonad&gt; hello... pls how can i download and extract music from youtube?
[03:58] > Gonad has left this channel.
[04:02] <Terry> Hm. An ask-and-run
```
