---
layout: post
title:  Motivation
date:   2013-10-27 17:06:25
categories: jekyll update
---

Invoicing saves you from worrying about ugly things like tax, book-keeping
and invoice generation when working on your Rails app. It handles many
currencies out of box, special tax rules and mostly just works and remains
extensible should you choose to extend it.

# <a name="installation"> Installation </a>

`Invoicing` gem works with Rails 3 or Rails 4. You can get started by adding
it to your Gemfile.

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


This command creates default migrations and models required to get you started.
Run migrations and you are good to go.

{% highlight bash %}
~> bundle exec rake db:migrate
{% endhighlight %}

# Usage - Quick Guide.

`Invoicing` gem works on the concept of [ledger](http://en.wikipedia.org/wiki/Ledger).
It assumes that each account has a ledger of items, which contain lots of invoices,
credit notes and payment notes. For eg: If you are running a company, your company
will have one ledger which contains entries for invoices that you send to clients,
end users etc. Each ledger item can have 1 or more line items. All the taxes will
be applied on inventory which constitute line items.

Lets work through a scenario and see how you can generate a simple invoice.

{% highlight ruby %}
# assuming that you have models for company, user and products or services
company = Company.new(name: 'My Company')
user    = User.new(name: 'Awesome client', email: 'awesome@example.com')
product = Product.new(name: 'T-Shirts', price: 10)

# create an invoice, and add line items to them
invoice = InvoicingLedgerItem.new(sender: company, recipient: user)
invoice.line_items.build(description: product.name,
                         net_amount: product.price,
                         tax_amount: 0)
invoice.save

# have a mailer, which sends invoice to user
# InvoiceMailer.send_invoice(invoice).deliver
{% endhighlight %}

You can customize the system so that you can specify types of ledger items {
like invoice, credit note, payment note}, different currencies on ledger items,
evolving tax rates, statuses on ledger items etc.
