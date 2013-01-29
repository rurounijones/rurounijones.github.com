---
layout: post
title: "Textile filtering with RedCloth"
date: 2009-10-24 18:00
comments: false
categories: [ruby]
description: How to monkey-patch RedCloth to allow you to filter allowed HTML.
             Written from a Ruby on Rails perspective but can also be used in
             other frameworks
keywords: textile redcloth filter filtering
published: true
---

I wanted to use RedCloth to let users of my rails app make textile formatted
posts but I wanted to restrict the input they were allowed to use. How to do 
this? I thought it would be simple.

**Warning: This article was imported from an old site and is therefore itself 
rather old. It may not still be accurate for current versions of RedCloth.**

<!--more-->

Textile has a +filter_html+ option which I thought would do the trick but that
only filters what HTML RedCloth allows users to enter. It doesn’t filter the 
HTML created by Redcloth itself when a user uses textile tags.

So how to filter the textile tags?

First, assuming you are using Rails 2.3 or later create the following file. 
For other frameworks please use the recommended method for adding start-up 
code to that framework.

```
config/initializers/redcloth_filter.rb
```

This file will be run during the rails initialization and will contain the code
we want to override (monkey-patch). Now paste the following code into the file.

``` ruby config/initializers/redcloth_filter.rb
module RedCloth::Formatters::HTML
  include RedCloth::Formatters::Base

  ALLOWED_TAGS = {
      'a' => ['href', 'title'],
      'br' => [],
      'i' => nil,
      'u' => nil,
      'b' => nil,
      'pre' => nil,
      'kbd' => nil,
      'code' => ['lang'],
      'cite' => nil,
      'strong' => nil,
      'em' => nil,
      'ins' => nil,
      'sup' => nil,
      'sub' => nil,
      'del' => nil,
      'table' => nil,
      'tr' => nil,
      'td' => ['colspan', 'rowspan'],
      'th' => nil,
      'ol' => ['start'],
      'ul' => nil,
      'li' => nil,
      'p' => nil,
      'h3' => nil,
      'h4' => nil,
      'h5' => nil,
      'h6' => nil,
      'blockquote' => ['cite'],
  }
    
  def after_transform(text)
    text.chomp!
    clean_html(text, ALLOWED_TAGS)
  end    
end
```

ALLOWED_TAGS is a hash of tags that you want to allow. You can take the 
[BASIC_TAGS](http://redcloth.rubyforge.org/classes/RedCloth/Formatters/HTML.html)
to use as a base and strip tags you don’t want to allow from the hash and add 
other ones if you want to.

So we have defined the tags that we want to allow. Now we need to actually do 
some stripping. This is where the after_transform method comes in. This is 
called by RedCloth as standard after initial modification. So what we can do is
override the method and tell RedCloth to clean_html again with the HTML string 
it has just created. To give you a list of steps.

* RedCloth is configured with +filter_html+ enabled
* User enters string (Textile and HTML)
* RedCloth strips HTML tags from the string according to the BASIC_TAGS using 
  clean_html method
* RedCloth converts the textile tags in the string to HTML

At this point the HTML’ised string is usually returned; however we do some 
overriding so that:

* RedCloth strips HTML tags from the above generated HTML string according to 
  our ALLOWED_TAGS using the clean_html method
* RedCloth returns the twice filtered HTML string.

Thinking about it you don’t even need +filter_html+ since it will all be
filtered the second time around explicitly by our code. However I feel a little
more secure by stripping all the user generated HTML cruft first using 
+filter_html+ before stripping our textile generated HTML ourselves.

Enjoy
