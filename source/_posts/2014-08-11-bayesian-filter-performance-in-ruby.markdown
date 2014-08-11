---
layout: post
title: "Bayesian Filter Performance in Ruby"
date: 2014-08-11 12:00
comments: false
categories: [ruby]
keywords: ruby performance stackprof bayesian
published: true
---

A while ago I was in an online discussion regarding "Correct Tools for
the Jobs" and how ruby was not a good language for developing say, an
operating system.

Someone made a comment that it was not a good idea to use ruby for
bayesian filtering of things like forum posts. (Bayesian filtering is
one of the primary algorithms used for determining if an email or forum
post is spam or not).

They made some performance claims which seemeds exceedingly slow, but
also made some statements that made me suspect their application design
was not all that it could be so I thought I would see if ruby was the 
guilty party.

This blog post is probably most helpful to low to intermediate ruby
developers.

<!--more-->

## TL;DR

In this post I will write about:

* Investigating to see if ruby can bayesian filter 1000 posts / second
* Finding a Bayesian filter
* Finding an appropriate data-set
* Writing some code to allow us to benchmark things
* Identifying performance bottlenecks using the stackprof gem and
  fixing them.
* Great success!
* "I learned something today"

If you are here for the performance analysis stuff with Stackprof then
you can skip down to "The Analysing" section below.

## The Claim

So first we need to identify the claims made against ruby, this will
determine what our goals are.

The poster made the following claims (Paraphrased slightly):

* "Implemented a Bayesian Filter to help deal with forum spam"
* "Tested under load"
* "Sometimes took up to 15 seconds due to all the Array structures"
* "The same implementation in C never exceeded half a second"

Now, if you are a ruby person the third claim might have been a red
flag for you (It was for me). "All the Array Structures"... Now when
it comes to searching, Arrays are slow, dog slow. A quick look at
our farvourite [Big-O Notation Cheetsheet](http://bigocheatsheet.com/)
site shows that searching an array is an "O\*N" operation. This means
that search time increases as the size of the array increases.

The other thing is that Arrays shouldn't really feature that much in
Bayesian filtering as the algorithm doesn't really care if a word
appears once or a hundred times.

These were the things that made me wonder if the poster was correct
in blaiming ruby for the slowness. I also happened to know that there
are some bayesian filter gems in the ruby-ecosystem which doesn't
really make sense if it is as slow as the poster claims.

When I pointed out the fact that it might be the poster's design they
didn't take it very well. They stated the challenge to create a userland
ruby application that processes 1000 unique variable length posts a
second. Their training data set was 100,000 posts.

## The Goal

So, now we know the claims we need the goal.

* Benchmark a ruby Bayesian filter.
* Try and attain a filtering rate in the neighbourhood of 1000 posts
  / second.
* Do the above with a training data-set of around 100,000 items.

## The Filter

Right sports fans! The first thing we need to do is write a bayesian
filter in ruby... Bwahahahahah, I make myself laugh. I am a great
believer in standing on the shoulders of giants so instead of writing
a filter lets find some likely ruby gems.

Some searching on github and "The Ruby Toolbox" lead me to the
[Classifier](https://github.com/cardmagic/classifier) gem, however
the last commit was a bit old and there was
[this](https://github.com/cardmagic/classifier/issues/4) issue which
called Classifier's accuracy into question which, at the time, was
not fixed.

Now, at this point I should confess, I only have a vague overview
level of knowledge on how bayesian filtering works and I was hoping
to spare my brain-power by not learning the nitty gritty mathematical
details (One of the reasons I was looking for a Gem in the first place)

However, the poster of that issue wrote their own gem instead called
[Ankusa](https://github.com/bmuller/ankusa) which looks pretty good.
So lets go with that.

## The Dataset

So now we have our filter we just need a data-set. The original poster
said that they used a training data set of 100,000 posts. I am going
to assume a roughtly 50/50 split here and say they had 50,000 known
good posts (from now on called 'ham') and 50,000 spam posts.

After some [DuckDuckGo](https://duckduckgo.com)'ing I found some
likely sources of curated spam/ham data-sets. It looked like at least
one good thing came out of the
[Enron Collapse](http://en.wikipedia.org/wiki/Enron_scandal) which is
that their emails were made public as part of the discovery process.

Some researchers then put them up for
[download](http://www.cs.cmu.edu/~enron/). While the numbers aren't
quite up to the 100,000 posts the original poster said they are
in the same ballpark.

* 19,089 ham emails
* 32,989 spam emails

For a grand-total of around 50,000 emails.

Wow, 750 odd words into this article and we haven't even gotten to the
beginning of the code stuff yet.

## Assumptions

Before I continue with the actual code stuff I am also going to have
to make some assumptions on how the filter was actually used. (At least
this is probably how I would have done it) I hope you will agree that
these are logical assumptions:

* A dedicated Bayesian machine: If you are processing 1000 forum
  posts / second I am going to assume that your operation is large enough
  to warrent a dedicated bayesian filtering server (Probably at the end of
  a REST API or message queue called by your front-end machines)

* No online updating of the training data-set: I will assume the training
  set is batch-updated at some point (Maybe a daily / weekly thing).

* The training data-set is held in memory: After implementating the
  training code using the Enron data-set I found that the training data only
  took up about 17MB of disk-space. Since this is pretty small and we assumed
  we have a dedicated server (See Assumption #1) that we will hold it in
  memory for maximum performance rather than a database.

## The Code

Before we can benchmark Ankusa we need some sort of test-runner code.
To that end I present to you
["Don't Bayes Me Bro"](https://github.com/rurounijones/dont_bayes_me_bro)
(DBMB: Well it made me chuckle when I named it and I like the idea of
spammers being tazered).

All code samples from here-on-out will come from either Ankusa or DBMB.

WARNING: This code is messy and not TDD'd and likely to make your 
"Beautiful Code" gland rupture.


### Training

The first thing we need to do is create our training data-set.

The actual code is [here](https://github.com/rurounijones/dont_bayes_me_bro/blob/master/lib/dont_bayes_me_bro/trainer.rb)

DBMB has a "training" folder into which we dumped the Enron emails from
earlier. All we are doing in this code recursively reading files from
the "spam" and "ham" sub-directories and training ankusa with them.

We then save the data to a file called "corpus". Note that since we
are doing an operation that uses file I/O we can speed things up by
creating one thread for "spam" and one for "ham". The GIL stops true
multi-threading but we can still get a performance speed-up in this
case.

Since the original poster was talking about "Forum Threads" I decided
to just parse the email body rather than everything including headers
etc.

This is kicked off from a Rake task.

### Benchmarking

Benchmarking is done [here](https://github.com/rurounijones/dont_bayes_me_bro/blob/master/lib/dont_bayes_me_bro/benchmarker.rb)
and is started from another Rake task which allows us to test 1,000
to 30,000 emails using the original data-set from which the corpus
came from.

To run the benchmark we insert the required number of email bodies
into a queue. We then pop from the queue and run the filter. Why a queue?
Well I was thinking down the road when multi-threading might make
an appearance.

A few things to note. To avoid skewing the benchmarks we pre-initialize
a two variables which are otherwise lazy loaded by Ankusa.

To keep things deterministic we save our queue data to a file for
future reading, this also saves time over parsing thousands of emails
each time. We also avoid emails with VERY short bodies (Less than 100
characters) to avoid getting an corpus with an overly short average
body length.

## The Analysis

Ok, here it is, the moment we have all been waiting for. After lots
of waffle about setting this up, it is time for the main event, the
actual benchmarking, the thing you are actually reading this blog post
about probably, the thing that is at the end of this overly long sentence
which is driving you insane!

Note: In the interest of brevity (In a post this long?! HAH) I have
removed a lot of unnecessary output from the commands.

Test Machine:

CPU: Intel(R) Core(TM) i7-2600 CPU @ 3.40GHz
OS: Ubuntu 14.04
Ruby: 2.1.2

```
$ rake benchmark:1000

Benchmarking 1000 emails
Starting benchmark
                 user     system      total        real
1000         6.260000   0.010000   6.270000 (  6.276492)
Jobs per second: 159
```
Oook then, roughly 159 jobs per second. 1/10th of what we need. This is
not the end of the world but it was a bit slower than I was hoping for.

So, first step is to analyse the code. For this we will use the godly,
amazing, idiot-proof [Stackprof](https://github.com/tmm1/stackprof) gem
by tmm1. Stackprof is introduced by tmm1
[here](http://tmm1.net/ruby21-profiling/) and I highly recommend reading
it.

So let us run the test again with stackprof enabled and see what we can
see.

``` bash
$ PROFILE=1 rake benchmark:1000

Benchmarking 1000 emails
Starting benchmark
                 user     system      total        real
1000         6.570000   0.020000   6.590000 (  6.593799)
Jobs per second: 151

$ stackprof /tmp/dbmb-20140811050715.dump --limit 5

==================================
  Mode: cpu(1000)
  Samples: 1597 (3.04% miss rate)
  GC: 101 (6.32%)
==================================
     TOTAL    (pct)     SAMPLES    (pct)     FRAME
      1321  (82.7%)        1137  (71.2%)     Ankusa::TextHash.valid_word?
       184  (11.5%)         111   (7.0%)     String#numeric?
        73   (4.6%)          73   (4.6%)     rescue in String#numeric?
        32   (2.0%)          32   (2.0%)     String#stem
        62   (3.9%)          30   (1.9%)     Ankusa::TextHash#add_word
```

Here I have told stackprof to identify the methods which were taking up
the most runtime and limit it to the longest 4 methods. And holy moly!

From reading line 16 we can tell that the vast VAST VAST majority of
the runtime is taken up with a single method! This is good and bad.
Good, in that if we can optimise this then it will be a huge win, bad in
that if we cannot, we are screwed. So let us look at the offending code.

``` ruby
# word should be only alphanum chars at this point
def self.valid_word?(word)
  not (Ankusa::STOPWORDS.include?(word) || word.length < 3 || word.numeric?)
end
```
[The full code](https://github.com/bmuller/ankusa/blob/782dba5601eafe83890faaae932177769eb5ffb7/lib/ankusa/hasher.rb#L20)

Hunh, ok, not that complicated. But the first part of this stands out.
`.include?` is used on enumerables. Enumerables like.... Arrays? Let us
check.

``` ruby

module Ankusa
  STOPWORDS = %W(lots-of-words)
end

```
[The full code](https://github.com/bmuller/ankusa/blob/782dba5601eafe83890faaae932177769eb5ffb7/lib/ankusa/stopwords.rb)

I have removed all the words from STOPWORDS but let me tell you it
had 544 entries. So what we have here is a 544 entry Array that is
searched for every.. SINGLE... WORD! Remember what we said about
searching arrays? O\*N average complexity, as the size of the array
increases so does the time it takes to search. We can show this using
a micro-benchmark.

The following code searches a set of arrays 5000 times.

``` bash
                                     user     system      total        real
one_word_array.include?          0.020000   0.000000   0.020000 (  0.018448)
ten_word_array.include?          0.030000   0.000000   0.030000 (  0.027409)
twenty_word_array.include?       0.040000   0.000000   0.040000 (  0.046323)
hundred_word_array.include?      0.180000   0.000000   0.180000 (  0.179623)
five_hundred_word_array.include? 0.320000   0.000000   0.320000 (  0.319819)
```

You can see how the time take to run `.include?` increases with the
size of the array.

So what are we to doooooo!? Well, when we have an array for the
sole purpose of calling `include?` on it we do not care about
duplicate values. Therefore we can use another, underused Ruby
Data-structure.

Tadaaaa! [Set](http://www.ruby-doc.org/stdlib-2.1.2/libdoc/set/rdoc/Set.html)
to the rescue! Sets are similar to Arrays with a few key differences.

But the big one, the BIG one, is that, unlike Arrays, Sets use the
same "Hash Table" data-structure as ruby Hashes to store their data.
What does this mean? Well another visit to
[Big-O Notation Cheetsheet](http://bigocheatsheet.com/) tells us that
Hash Tables are much MUCH better for searching with an average complexity
of O\*1 and a worst-case of O\*N.

This means that, even as the size increases the search-time remains
relatively constant. Let us test this again with our micro-benchmark.

``` bash
                                     user     system      total        real
one_word_set.include?            0.010000   0.000000   0.010000 (  0.010633)
ten_word_set.include?            0.020000   0.000000   0.020000 (  0.012516)
twenty_word_set.include?         0.010000   0.000000   0.010000 (  0.010707)
hundred_word_set.include?        0.010000   0.000000   0.010000 (  0.013273)
five_hundred_word_set.include?   0.010000   0.000000   0.010000 (  0.013702)
```
Sweeeeeeeeeet. So let us replace the Ankusa::STOPWORDS Array with a Set

``` ruby
require 'set'

module Ankusa
  STOPWORDS = Set.new %W(lots-of-words)
end

```

and see what happens.

``` bash
                 user     system      total        real
1000         1.950000   0.010000   1.960000 (  1.966099)

Jobs per second: 508
```

Wow, it just goes to show how a tiny, simple change can have such a huge
impact. A running time reduced from about 6 seconds to 2 seconds, we are
now halfway to our goal!

However halfway is not all the way. Improving performance is a simple cycle

* Benchmark
* Find bottleneck
* Fix bottleneck
* Start again

We have fixed out first bottleneck, let us find the next one using trusty
stackprof again.

``` bash
==================================
  Mode: cpu(1000)
  Samples: 498 (9.12% miss rate)
  GC: 102 (20.48%)
==================================
     TOTAL    (pct)     SAMPLES    (pct)     FRAME
       179  (35.9%)         105  (21.1%)     String#numeric?
        74  (14.9%)          74  (14.9%)     rescue in String#numeric?
        45   (9.0%)          44   (8.8%)     Ankusa::TextHash.atomize
        57  (11.4%)          40   (8.0%)     Ankusa::TextHash#add_word
        24   (4.8%)          24   (4.8%)     Set#include?
```
