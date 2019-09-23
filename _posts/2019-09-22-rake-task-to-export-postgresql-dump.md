---
layout: post
date: 2019-09-22 16:00
title:  "Rake: How to export PostgreSQL dump?"
category:
- tips
tags:
- rake
- rails
- postgresql
---

You may find this Rake task handy for generating PostgreSQL database dumps in a typical Rails project:

```ruby
namespace :db do
  desc "Dumps the database to db/APP_NAME.dump"
  task dump: :environment do
    raise("Could only be executed in development environment") unless Rails.env.development?

    with_config do |app, host, db, user|
      cmd = "pg_dump --host #{host} --username #{user} -Fp --verbose --clean --no-owner --no-acl --format=c #{db} > #{Rails.root}/db/#{app}.dump"
      puts cmd
      exec cmd
    end
  end

  private

  def with_config
    yield Rails.application.class.parent_name.underscore,
     'localhost',
      ActiveRecord::Base.connection_config[:database],
      ENV['USER']
  end
end
```

Although this may not work in case you have different database access configuration.
