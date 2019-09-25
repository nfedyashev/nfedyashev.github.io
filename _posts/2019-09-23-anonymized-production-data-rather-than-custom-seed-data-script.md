---
layout: post
date: 2019-09-23 16:00
title:  "An experiment: Anonymized production data rather than custom seed data script"
category:
- tips
tags:
- rake
- rails
- postgresql
---

This blog post explains the experiment of trying out an alternative approach to seed development data in a typical Rails application.

Click here if you want to skip the commonly used approaches and read about an [an alternative](http://sovereign.zone/tips/anonymized-production-data-rather-than-custom-seed-data-script.html#solution).

Two of the most commonly used patterns for development seed data that I've seen so far:

1) idempotent seed data script that doesn't generate unnecessary DELETE/INSERT SQL  queries, it doesn't assume your database is empty before you run this script. It is tolerant of the existing data and updates it(if necessary) to match the desired state.
Although this approach seems very purist and graceful there is a significant drawback that I experienced in real production projects. It is rather time-consuming to maintain that kind of seed data setup. That's something that you always have to keep in mind everytime you make some model/db schema changes.

2) FactoryBot models that generate all the necessary associations starting with a clean database.
This approach is much easier in maintenance in comparison with the first approach assuming your factories are up to date.

In one of my recent production project I've tried something else and greatly enjoyed the result.

Instead of spending time supporting manually written development seed data script, use an anonymized dump of a production database.

Since this is a rather uncommon and opinionated approach, and they always come in with their pros and cons, let's review them all.

<!--more-->

#### Cons:

* it is not going to work if you have a huge database
* initial anonymized seed data dump and all the future ones must be done in a secure way. You need to be very cautious of this and not compromise your production data.
* it might add some significant space to your git repository and slightly slow down your development setup time

#### Pros:

  * You don't need to spend time maintaining seed data and keeping them up to date. Just re-run the dump->anonymize task and you're good to go.
  * Since all developers know have that fresh/recent anonymized dump which they can safely use locally for debugging production issues. Depending on your team/project structure this side effect might be very helpful because all the developers can safely debug and investigate production issues locally without risking affecting real production users and *without need to have access to that info*. In most cases for debugging & investigation developers only need to know some IDs or request params which can be seen in Airbrake/Rollbar/Bugsnag/Sentry.
  * New developers onboarding become much easier. They can safely try locally any user, click any feature without risking of having any unintended side effects.
  * By using anonymized seed data for staging/pre-production environment you save even more time because you don't need to maintain it separately.

As you may notice anonymizer task is pretty simple because you can reuse your factories.

#### Solution:

```ruby
  namespace :db do
    desc "Anonymizes existing local database"
    task anonymize: :environment do
      raise("Could only be executed in development environment") unless Rails.env.development?

      except_user_emails = ["email.that@you.want.to.keep"]
      User.where.not(email: except_user_emails).each do |user|
        attributes = FactoryBot.attributes_for(:user)
        user.attributes = attributes.slice(:email, :name)
        user.authentication_token = Devise.friendly_token
        puts user.changes
        user.save!
      end

      AnonymousSecureModel.all.each do |asm|
        attributes = FactoryBot.attributes_for(:another_secure_model)
        asm.attributes = attributes.slice(:code, :name)
        asm.uuid = SecureRandom.hex[0..6]
        asm.save!
      end

      VerySecureModel
        .delete_all
    end
  end
```

[Blob post on how to export your anonymized dump](http://sovereign.zone/tips/rake-task-to-export-postgresql-dump.html)
