---
layout: post
date: 2016-03-15 22:00
title:  "Gemfile custom groups"
category: 
- docs
tags:
- rails
---

```
source 'https://rubygems.org'

gemfiles = [ 'Gemfile1', 'Gemfile2' ]
gemfiles.each do |gemfile|
  instance_eval File.read(gemfile)
end
```

Gemfile1:
```
source 'https://rubygems.org'

# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
gem 'rails', '4.0.1'

# Use sqlite3 as the database for Active Record
gem 'sqlite3'

# Use SCSS for stylesheets
gem 'sass-rails', '~> 4.0.0'

# Use Uglifier as compressor for JavaScript assets
gem 'uglifier', '>= 1.3.0'

# Use CoffeeScript for .js.coffee assets and views
gem 'coffee-rails', '~> 4.0.0'

# See https://github.com/sstephenson/execjs#readme for more supported runtimes
# gem 'therubyracer', platforms: :ruby

# Use jquery as the JavaScript library
gem 'jquery-rails'

gem 'pry'
```


Rails.groups(*groups)
Returns all rails groups for loading based on:

The Rails environment;

The environment variable RAILS_GROUPS;

The optional envs given as argument and the hash with group dependencies;

groups assets: [:development, :test]

# Returns # => [:default, :development, :assets] for Rails.env == “development” # => [:default, :production] for Rails.env == “production”



git:(relaxed) ✗ ag Bundler.require
config/application.rb
7:Bundler.require(*Rails.groups)
➜  lorega git:(relaxed) ✗ vim



groups = {
    # If you precompile assets before deploying to production, use this line
    assets:     %w(development test),
    monitoring: %w(staging production),
    security_analysis: %w(development test),
  }
  # List the groups that are always loaded, followed by the ones that are selectively loaded based on environment
  Bundler.require(:default, :rails3, :web_server, :data_store, :location, :async_jobs, :templates, :javascript, :users, :metrics, :messaging, :email, :enhancers, :mobile, Rails.env, *Rails.groups(groups))
  #Bundler.require(*Rails.groups(:assets => %w(development test)))
  # If you want your assets lazily compiled in production, use this line
  # Bundler.require(:default, :assets, Rails.env)



ruby '1.9.3'

#################### Rails 3.2 ####################
# NOTE on branching:  We are using the rails 4 track gems as much as possible,
#                     because in most cases they retain support for Rails 3,
#                     and there will fewer issues with upgrading to Rails 4 when the time comes.
#                     Where we need actual different gems (e.g. rails proper) we have a rails4 group (see EOF)
group :rails3 do
  gem 'rails', '~> 3.2.13'
  # Not compatible with Rails4, see :rails4 group for alternative
  gem 'simple_form', '~> 2.0.4'
  # Backports default Rails4 functionality to Rails3
  gem 'dismissible_helpers', '~> 0.1.5'
end
# Gems used only for assets and not required
# in production environments by default.
group :assets do
  # Implant jQuery directly into the Asset Pipeline ========>
  gem 'jquery-rails', '~> 2.2.0' # updates to jQuery 1.9, which is not yet compatible with foundation, see:
  gem 'compass-rails', '>= 1.0.3' # github: 'ai/compass-rails', branch: 'rails4' #
  gem 'compass-h5bp', '~> 0.1.1', require: false
  gem 'sprockets-rails', '~> 0.0.1'
  gem 'sass-rails', '~> 3.2.5'
  gem 'coffee-rails', '~> 3.2.2'
  gem 'turbo-sprockets-rails3', '~> 0.3.6'
  # AWS RUBY APIs
  # NOTE: This gem is almost threadsafe
  # See: https://github.com/mperham/sidekiq/wiki/Problems-and-Troubleshooting
  gem 'aws-sdk', '~> 1.8.5'
                                 # Assets stored on AWS S3
                                 # Goal is to send compiled assets to AWS when FOGGY is not set, and when it is set to 'true'
  gem 'asset_sync', '~> 0.5.4', {}.merge(!ENV['FOGGY'].nil? && ENV['FOGGY'] != 'true' ? {require: false} : {})
  gem 'uglifier', '~> 2.0.1', require: false
  gem 'zurb-foundation', '>= 4.1.1' # github: 'zurb/foundation'
  gem 'foundation-icons-sass-rails', '>= 2.0.0'
end
#################### /Rails 3.2 ####################

group :web_server do
  # The Webserver
  gem 'puma', '~> 2.0.0.b7', require: false
  # Allows rack to rewrite requests, no need for Apache mod_rewrite
  #gem 'rack-rewrite', '~> 1.3.3'
end

group :data_store do
  # The Database
  gem 'pg', '~> 0.15.1'
  # More power to the Database
  gem 'pg_power', '~> 1.3.1' #, github: 'acquaintable/pg_power', branch: 'rails4'
  # Allows PostgreSQLto use the new HSTORE datatype supported in Rails 4
  gem 'activerecord-postgres-hstore', '~> 0.7.5'
  # Allows PostgreSQL to use the new ARRAY datatype supported in Rails 4
  gem 'activerecord-postgres-array', github: 'tlconnor/activerecord-postgres-array'
  # Bit fields for Active Record - Follows conventions of standard boolean columns.
  gem 'flag_shih_tzu', '~> 0.3.2'
  # Neo4j REST API
  gem 'neography', github: 'maxdemarzi/neography' #, '~> 1.0.5'
  # Replicates wholly, or partially, ActiveRecord (PG) models into Neo4j for faster querying
  # Manages synchronization of updates to records across both data stores
  gem 'neoid', '~> 0.1.2' #, path: '/Users/pboling/Documents/src/acq/neoid'
  # Versioning Things and Soft-Deletes
  gem 'vestal_versions', github: 'justintanner/vestal_versions'
  # I can has Vote? Voting On Things is fun

  gem 'thumbs_up', github: 'bouchard/thumbs_up' #'~> 0.6.2'
  # Map any methods on any Ruby objects to a CSV export, and vice verse.
  gem 'csv_pirate', '~> 5.0.7'
  # adds slugs to records
  gem 'friendly_id', '~> 4.0.9'
end

group :location do
  # Geolocations
  gem 'geokit', '~> 1.6.5'
  gem 'geokit-rails3', github: 'acquaintable/geokit-rails3'
  gem 'geocoder', '~> 1.1.6'
end

group :async_jobs do
  # The Cache - Redis can back Object Caches, I18n, Session, HTTP Cache
  gem 'redis', '~> 3.0.3', require: false
  gem 'redis-namespace', '~> 1.2.1', require: false
  gem 'redis-rails', '~> 3.2.3', require: false
  gem 'redis-store', '~> 1.1.3', require: false

  # Our Queue
  gem 'sidekiq', github: 'mperham/sidekiq' #'~> 2.9.0'
  # Add failure tracking and failure tab to sidekiq UI
  gem 'sidekiq-failures', '~> 0.1.0', require: false
  # Worker Manager (Hires and Fires workers)
  gem 'autoscaler', '~> 0.2.1', require: false
  # Alternate Worker manager (Hires and Fires workers)
  gem 'hirefire-resource', '~> 0.0.2'
  # track your jobs enabling reporting of progress per job
  gem 'sidekiq-status', '~> 0.3.0', require: false
end

group :templates do
  # a template engine
  gem 'tilt', '~> 1.3.6', require: false # => Heroku all of a sudden can't find 1.3.5
  # View Template Language
  gem 'haml', '>= 4.0.0'
  #gem 'haml-rails', '~> 0.4'
  # View Template Language
  gem 'slim', '~> 1.3.7'
  # the default generator would is now slim.  Views can be written in haml or slim.
  gem 'slim-rails', '~> 1.1.1'
  # Views can be and should be 100% DRY
  gem 'dry_views', '~> 0.0.2'
  # Markdown Parser (for copy)
  gem 'redcarpet', '~> 2.2.2', require: false
  # To avoid indecent exposure of instance variables in the view
  #gem 'decent_exposure', '~> 2.0.1'
  #gem 'cells', '~> 3.8.6' #, :path => '/Users/pboling/Documents/src/other/cells' #:git => 'git://github.com/acquaintable/cells.git'
end

# TODO: Determine which of these can be moved into :assets group
group :javascript do
  # Base for JSON
  gem 'json', '~> 1.7.7', require: false
  # Fast JSON Object parsing and marshalling
  gem 'oj', '~> 2.0.10', require: false
  # https://github.com/zurb/foundation/pull/1432
  # Enable the AMD design pattern for Javascript modules and integrate into the Asset Pipeline ========>
  gem 'requirejs-rails', github: 'acquaintable/requirejs-rails'
  # moved out of assets group due to config/initializers/handlebars_assets.rb
  gem 'handlebars_assets', github: 'acquaintable/handlebars_assets' # '~> 0.12.0' #  path: '/Users/pboling/Documents/src/acq/handlebars_assets'
end

group :users do
  # Registration, Authentication, Invitation, Confirmation
  gem 'devise', '~> 2.2.3'
  gem 'devise_invitable', github: 'scambra/devise_invitable'
  gem 'devise-async', '~> 0.7.0'
  # Facebook Oauth
  gem 'koala', '~> 1.6.0'
  gem 'omniauth', '~> 1.1.4', require: false
  gem 'omniauth-facebook', '~> 1.4.1'
  # Authorization
  gem 'cancan', '~> 1.6.9'
  gem 'rolify', '>= 3.2.0'
  # Stateful Behavior
  gem 'state_machine', '~> 1.2.0'
end

group :metrics do
  # User Metrics
  gem 'km', '~> 1.1.3'
  # Send errors to airbrake.io
  gem 'airbrake', '~> 3.1.7'
  # Report missing i18n translations
  gem 'i18n-airbrake', github: 'acquaintable/i18n-airbrake', branch: 'configuration_options'
  # Code Metrics
  gem 'newrelic_rpm', '>= 3.6.0.83'
  # The rails app administration tool
  gem 'rails_admin', github: 'sferik/rails_admin' #'~> 0.4.3'
  # Debugging toolbar, heir of rack-bug
  #gem 'rack-insight'
end

group :messaging do
  # User in-app messaging
  gem 'acts-as-messageable', '~> 0.4.8'
  # Add messages to your flash messaging stack whenever you need to, array-style
  gem 'stackable_flash', '~> 0.0.7'
  # Use the cookie for transporting the flash messages and javascript to render them into the view
  #gem 'cacheable_flash', '~> 0.3.2'
end

group :email do
  # Third Party Email Service
  gem 'sendgrid', '~> 1.1.0', require: false
  # Redirect all email in non-Production environments to alternate, safe, addresses
  gem 'sanitize_email', '~> 1.0.5'
end

group :enhancers do
  # Turbolinks makes following links in your web application faster. Read more: https://github.com/rails/turbolinks
  gem 'turbolinks', '~> 1.1.1'
  # Mini Controllers & tiny mounted Rack apps inside Rails for widgety things
  # XML Parsing
  gem 'nokogiri', '~> 1.5.9', require: false
  # Yet another framework
  # The sidekiq admin panel is a sinatra app
  gem 'sinatra', '~> 1.3.6', require: nil
  # Possessive form of a string
  gem 'possessive', '~> 1.0.1', require: false
  # Convert numbers to words 1 => 'one'
  gem 'numbers_and_words', '~> 0.6.0', require: false
  # memoize methods - Extracted from Rails' ActiveSupport::Memoizable in June 2011
  gem 'memoist', '~> 0.9.0'
  # Queryable arrays, for first: pages[name: 'About'], for all: pages[[published: true]]
  gem 'queryable_array', '~> 0.0.1', require: false
  # For advanced transformation of queues / lists
  gem 'qfill', '~> 0.0.3', require: false
  # faker allows the demo to seem realistic
  gem 'faker', '~> 1.1.2'
  # chronic is a natural language time parser
  gem 'chronic', '~> 0.9.1'
end

# TODO: Enable mobile push
group :mobile do
#  # Mobile Push
#  gem 'urbanairship', '~> 2.3.1'
end

group :security_analysis do
  # Check for Security Holes
  gem 'brakeman', '~> 1.9.5', require: false #,github: 'presidentbeef/brakeman' #'~> 1.8.2'
end

group :development do
  # Local dev webserver, mimics Heroku deployed environment
  gem 'foreman', '~> 0.61.0', require: false
  gem 'localtunnel', '~> 0.3', require: false
  # Find bad queries, TODO: evaluate: uniform_notifier for use with bullet
  gem 'bullet', '~> 4.5.0'
  gem 'quiet_assets', '~> 1.0.2'
  # Improve the development error experience
  gem 'better_errors', '>= 0.3.2'
  gem 'binding_of_caller', '~> 0.7.1', require: false
  # Annotate models, fixtures, and model specs
  gem 'annotate', '>= 2.5.0', require: false
  gem 'mailcatcher', require: false, github: 'teeparham/mailcatcher' #'~> 0.5.10'
  # Allows us to magically convert directories of haml templates to slim templates
  #gem 'haml2slim'#, '>= 0.4.6'
end

group :development, :test do
  #gem 'powder', '~> 0.2.0'
  # How will we debug all the things?
  gem 'debugger', '~> 1.5.0', require: false
  gem 'rails_best_practices', '~> 1.13.4', require: false
  #gem 'ruby-prof', '~> 0.11.3', require: false
  gem 'rspec-rails', '>= 2.12'
  gem 'fabrication', '~> 2.6.4', require: false
  gem 'parallel_tests', '~> 0.10.4'
  gem 'teabag', github: 'acquaintable/teabag'
  gem 'pry', '~> 0.9.10'
  gem 'pry-rails', '~> 0.2.2'
  gem 'pry-doc', '~> 0.4.5', require: false
  gem 'pry-remote', '~> 0.1.7', :require => 'pry-remote'

  # Guard allows for local continuous integration as changes are made to files.
  gem 'guard', '~> 1.7.0', require: false
  gem 'guard-spork', '~> 1.4.3', require: false
  gem 'guard-rspec', '~> 2.5.2', require: false
  gem 'rb-inotify', '~> 0.9.0', require: false
  gem 'rb-fsevent', '~> 0.9.3', require: false
  gem 'wdm', :platforms => [:mswin, :mingw], require: false
  # Mac OS X Notification center
  gem 'terminal-notifier-guard', '~> 1.5.3', require: false
  # Spork
  gem 'spork-rails', '~> 3.2.1'
  # Ensures that .env will be loaded into the environment even when outside foreman (e.g. rake, rails console)
  gem 'dotenv-rails', '~> 0.6.0', require: false
end

group :test do
  # Simplifies testing with helpful macros and matchers
  #gem 'remarkable_activerecord', '>=4.0.0.alpha2'
  gem 'cane', '~> 2.5.2', require: false
  gem 'test-unit', '~> 2.5.4', require: false
  gem 'mocha', '~> 0.13.2', require: false
  #gem 'rspec-apotomo', :git => 'git://github.com/kristianmandrup/rspec-apotomo.git', :ref => '03a2ac2f526fa668ee3a4bdb09cbc41693931ca3'
  gem 'rspec-cells', github: 'acquaintable/rspec-cells'
  gem 'shoulda-matchers', '~> 2.0.0', require: false
  gem 'bourne', '~> 1.4.0', require: false
  gem 'capybara', '~> 2.1.0', require: false
  gem 'capybara-email', require: false, github: 'aaronchi/capybara-email', branch: 'patch-1'
  gem 'poltergeist', require: false, github: 'brutuscat/poltergeist'
  gem 'vcr', '~> 2.3.0', require: false
  gem 'fakeweb', '~> 1.3.0', require: false
  #gem 'autotest', '4.4.6'
  #gem 'autotest-rails', '4.1.2'
  gem 'simplecov', '~> 0.7.1', require: false
  gem 'email_spec', '~> 1.4.0', require: false
end

# These gems are only loaded when rails is loaded in console mode, see condition near top of config/application.rb
group :console do
  # Allows database truncation. Simple::Aplication.wipe from the console
  gem 'database_cleaner', '~> 0.9.1', require: false
  # Evaluates gems to see if they should be required by bundler at app boot
  gem 'gem_bench', '~> 0.0.4', require: false
  gem 'irbtools', require: false, github: 'acquaintable/irbtools'
  gem 'irbtools-more', '~> 1.4.0', require: false
  gem 'terminal-notifier', '~> 1.4.2', require: false
end

#################### Gems Under Evaluation ####################
# This group is never loaded by default in any environment
# We are in process of evaluating them, and this ensures they are bundle-able,
#   for at least a nominal level of integration awareness
group :evaluation do
  # HTML5 Boilerplate
  #gem 'html5-rails', '~> 0.1.0'

  # One of the gems we use includes a yanked version of net-scp,
  # this forces the use of an existing version
  #gem "net-scp", "1.0.4"
  #
  ## If/When we start versioning our API we should use version cake!
  ##gem 'versioncake'
  #
  ## Abstraction Layer over the WebSocket protocol
  #gem 'websocket', '~> 1.0.6'
  ##gem 'airbrake-statsd', '~> 0.2.1'
  ## App, Service & Environment Config
  #gem 'figaro', github: 'acquaintable/figaro', branch: 'rails4'
  ## User Metrics
  #gem 'analytical', '~> 3.0.12'
  ## But User Metrics from the client-side!
  #gem 'event_tracker', github: 'acquaintable/event_tracker'
  ## AJAX file upload
  #gem 'remotipart', '~> 1.0.5'
  ## Automated Sitemap and SEO
  #gem 'sitemap_generator', '~> 3.4'
  ## A/B Testing
  #gem 'split', '~> 0.6.0'
  ## Mobile Response
  #gem 'mobylette', '~> 3.3.2'
  ## Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder
  #gem 'jbuilder', '~> 1.2.0'

  # To use dummy images built on-demand for rapid prototyping
  # Depends on an install of imagemagick and the rmagick gem
  # TODO:Verify Setup instructions for ImageMagick before uncommenting rmagick
  #gem 'rmagick'
  #gem 'dummy_image', :git => 'git://github.com/davelyon/dummy_image.git'

  # We might switch back to Unicorn at some point...
  # TODO: evaluate puma vs. unicorn
  #gem 'unicorn', '~> 4.6.0'
end
####################/ Gems Under Evaluation ####################

#################### Rails 4.0 ####################
#group :rails4 do
#  gem 'rails',     github: 'rails/rails'
#  gem 'journey',   github: 'rails/journey'
#  gem 'arel',      github: 'rails/arel'
#  gem 'simple_form', github: 'plataformatec/simple_form', branch: 'rails_4'
#  gem 'activerecord-session_store', github: 'rails/activerecord-session_store'
#  TODO: These are deprecated functionality of Rails 3 removed from Rails4
#         We will use this to transition, but ultimately change our code to not rely on it
#  gem 'activerecord-deprecated_finders', github: 'rails/activerecord-deprecated_finders'
#  gem 'protected_attributes', github: 'rails/protected_attributes'
#end
#group :assets do
#  gem 'sprockets-rails', github: 'rails/sprockets-rails'
#  gem 'sass-rails',   github: 'rails/sass-rails'
#  gem 'coffee-rails', github: 'rails/coffee-rails'
#end
#################### /Rails 4.0 ####################

