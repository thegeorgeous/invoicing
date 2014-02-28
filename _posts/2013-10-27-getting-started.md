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

What invoicing gem can do for you?

1. Store any number of different types of invoice, credit note and payment
   record
2. Represent customer accounts, supplier accounts, and even complicated
   multi-party billing relationships
3. Automatically format currency values beautifully
4. Automatically round currency values to the customary precision for that
   particular currency, e.g. based on the smallest coin in circulation
5. Support any number of different currencies simultaneously
6. Render invoices, account statements etc. into HTML (fully styleable and
   internationalisable)
7. Export into the UBL XML format for sharing data with other systems
8. Provide you with a default Value Added Tax (VAT) implementation, but you
   can also easily plug in your own tax logic
9. Dynamically display tax-inclusive or tax-exclusive prices depending on
   your customer's location and preferences
10. Deal with tax rates or prices changing over time, and automatically
    switch to the new rate at the right moment
11. Efficiently summarise account balances, sales statements etc. under
    arbitrary conditions (e.g. data from one quarter, or payments due at a
    particular date)

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
user    = User.new(name: 'Awesome Customer', email: 'user@example.com')
product = Product.new(name: 'T-Shirt', price: 10)

# create an invoice, and add line items to them
invoice = InvoicingLedgerItem.new(sender: company, recipient: user)
invoice.line_items.build(description: product.name,
                         net_amount: product.price,
                         tax_amount: 0)
invoice.save

# have a mailer, which sends invoice to user
# InvoiceMailer.send_invoice(invoice).deliver
{% endhighlight %}

You can customize the system so that you can specify types of ledger items {like
invoice, credit note, payment note}, different currencies on ledger items,evolving
tax rates, statuses on ledger items etc.

# Getting Started
## Creating a structure
Genrally, there will be many types of invoices that a company typically sends, or
receives. This gem heavily depends on STI, so, its better to create an structure
that works well for different types of invoices, credit notes, and payment notes.
Its always advised to have a base classes for above said types.

{% highlight ruby %}
class Invoice < InvoicingLedgerItem
  acts_as_invoice
end

class CreditNote < InvoicingLedgerItem
  acts_as_credit_note
end

class PaymentNote < InvoicingLedgerItem
  acts_as_payment
end
{% endhighlight %}

Now, you can further sub class them as per your need. Say, you want to send an
invoice to your client, subclass `Invoice`

{% highlight ruby %}
class EndUserInvoice < Invoice
  belongs_to :recipient, class_name: 'User'
end

class ClientInvoice < Invoice
  belongs_to :recipient, class_name: 'Client'
end
{% endhighlight %}

This way, you can generate different types of invoices and customize them as
per your need. Based on the context, the type of invoice that gets created
will differ.

## Creating an invoice
As mentioned in quick guide, creating invoice is as simple as adding bunch of
line items to invoice.

{% highlight ruby %}
invoice = EndUserInvoice.new(sender: company, recipient: user)
invoice.line_items.build(description: 'Goodies: T-Shirt',
                         net_amount: 10, tax_amount: 0)
invoice.line_items.build(description: 'Goodies: Coffee Cup',
                         net_amount: 20, tax_amount: 1.3)
invoice.save
{% endhighlight %}

Some of the important things to note here:

1. Invoices are immutable. Once created, its not advised to modify them.
   Created a wrong invoice? Send a credit note if you have charged in
   excess, or send another invoice if you have charged less.
2. invoicing gem doesn't add any validations. All the validations are
   responsibility of your application. For eg: Generating invoice without
   sender or recipient is perfectly allowed by this gem, although it
   doesn't make any sense.
3. Its not necessary to associate line items with goods that are being
   sold to recipient. As you can see, line items dont care what **item**
   is being added to invoice, it doesn't care whether a goodie called
   `T-Shirt` exists in the system or not.

## Sending an invoice
This gem provides generation of pdf from an invoice. For pdf generation to work,
you need to include `prawn` gem as dependency. Add it to your Gemfile.

{% highlight ruby %}
  gem 'prawn'
{% endhighlight %}

Now, you can generate pdf from invoice like this:

{% highlight ruby %}
require 'invoicing/ledger_item/pdf_generator'

# generate, and save the invoice
@invoice = InvoicingLedgerItem.new()
# ...

pdf_creator = Invoicing::LedgerItem::PdfGenerator.new(@invoice)
pdf_file = pdf_creator.render Rails.root.join('/path/to/pdf')
{% endhighlight %}

Now, use your favorite action mailer to send this pdf to your client.


# Developer Guide - Detailed Documentation

`Invoicing` gem uses `acts-as` style, and provides various functionalities:

1. ledger item, it can be an invoice, a credit note, or a payment note.
2. line item, which specifies an entry in a ledger item.
3. tax rates, which can be used to caculate taxes on various items.

Code contains detailed documentation, but there are underlying concepts which
should be understood first:

1. `acts_as_cached_record`: tables like tax rates, will have very less number of
   entries. Since there will be very less number of entries, then can be cached
   and returned from the cache, rather than querying from the database again
   and again. `acts_as_cached_record` helps in caching records. This module will
   be deprecated soon.

2. `class_info.rb`: this file is the heart of all the `acts_as` modules. It helps
   in storing information related to all the instances of a class at class level.
   It also takes care of inheritance. It defines basic `acts_as` helper, which is
   inturn used by other modules.

3. `connection_adapter_ext.rb`: It tries to generate specific statements and queries
   according to database adapter used, which are not inherently supported by AR.
   This module will also be deprecated soon.

4. `currency_value`: Helpers for effectively storing and formatting monetary values.

5. `find_subclasses`: This module is more or less a hack, which helps in optimizing
   queries when a hierarchy of classes exist. This module will be deprecated soon.

6. `ledger_item`: This is the heart of 
