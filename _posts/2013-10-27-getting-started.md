---
layout: post
title:  Motivation
date:   2013-10-27 17:06:25
categories: jekyll update
---

Invoicing saves you from worrying about ugly things like tax, book-keeping and invoice generation
when working on your Rails app. It handles many currencies out of box, special tax rules and
mostly just works and remains extensible should you choose to extend it.

# <a name="installation"> Installation </a>

`Invoicing` gem works with Rails 3 or Rails 4. You can get started by adding it to your Gemfile.

{% highlight ruby %}
gem 'invoicing'
{% endhighlight %}
After that you can either use generators to create necessary tables and models
or you can do so manually.

You can run generator using command:

{% highlight bash %}
~> bundle exec rails generate invoicing
   create  db/migrate/20131231043450_invoicing_tax_rates.rb
   create  app/models/invoicing_tax_rate.rb
   create  db/migrate/20131231043451_invoicing_ledger_items.rb
   create  app/models/invoicing_ledger_item.rb
   create  db/migrate/20131231043452_invoicing_line_items.rb
   create  app/models/invoicing_line_item.rb
{% endhighlight %}
