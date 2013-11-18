Ruby Invoicing Framework
========================
[![Build Status](https://travis-ci.org/code-mancers/invoicing.png?branch=master)](https://travis-ci.org/code-mancers/invoicing)
[![Code Climate](https://codeclimate.com/github/code-mancers/invoicing.png)](https://codeclimate.com/github/code-mancers/invoicing)
[![Coverage Status](https://coveralls.io/repos/code-mancers/invoicing/badge.png)](https://coveralls.io/r/code-mancers/invoicing)
[![Dependency Status](https://gemnasium.com/code-mancers/invoicing.png)](https://gemnasium.com/code-mancers/invoicing)
[![Gem Version](https://badge.fury.io/rb/invoicing.png)](http://badge.fury.io/rb/invoicing)

* [Website](http://ept.github.com/invoicing/)
* [API Docs](http://rubydoc.info/github/ept/invoicing/frames/)
* [Code](http://github.com/ept/invoicing/)

# Description

This is a framework for generating and displaying invoices (ideal for
commercial Rails apps). It allows for flexible business logic; provides tools
for tax handling, commission calculation etc. It aims to be both
developer-friendly and accountant-friendly.

The Ruby Invoicing Framework is based on
[ActiveRecord](http://api.rubyonrails.org/classes/ActiveRecord/Base.html).

Please see [the website](http://ept.github.com/invoicing/) for an introduction
to using Invoicing, and check the
[API reference](http://invoicing.rubyforge.org/doc/) for in-depth details.

If you're interested in contributing to the invoicing gem itself, please see the file
[HACKING.md](http://github.com/ept/invoicing/blob/master/HACKING.md).

# Features

* TODO

# Requirements

* ActiveRecord >= 2.1
* Only MySQL and PostgreSQL databases are currently supported

# Getting Started

Add the following to your Gemfile:

    gem "invoicing"

And run `bundle install` to install the gem.

Run the following from root of your Rails project to setup migrations and models
for invoicing to work :

    bundle exec rails g invoicing

Run the generated migrations :

    bundle exec rake db:migrate

# Status

So far, the Ruby Invoicing Framework has been tested with ActiveRecord 2.2.2,
MySQL 5.0.67 and PostgreSQL 8.3.5. We will be testing it across a wider
variety of versions soon.

# Credits

The Ruby invoicing framework originated as part of the website
[Bid for Wine](http://www.bidforwine.co.uk), developed by Patrick Dietrich,
Conrad Irwin, Michael Arnold and Martin Kleppmann for Ept Computing Ltd.
It was extracted from the Bid for Wine codebase and substantially extended
by Martin Kleppmann. 

# LICENSE

Copyright (c) 2009 Martin Kleppmann, Ept Computing Limited.

This gem is made publicly available under the terms of the MIT license.
See LICENSE and/or COPYING for details.
