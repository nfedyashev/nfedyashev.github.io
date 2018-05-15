---
layout: post
date: 2018-05-14 12:36
title: "Rails Generator"
comments: true
category: 
- testing
tags:
- rails
---

38  lib/generators/bigco/templates/active_record_model.rb
+# =============================================================================================
￼ +#
￼ +#                       _____ _    
￼ +#                      / ____| |   
￼ +#                     | |    | | __ 
￼ +#                     | |    | |/ _
￼ +#                     | |____| | (_
￼ +#                      \_____|_|\__
￼ +#
￼ +#
￼ +#   <%= file_name %>.rb
￼ +#
￼ +# =============================================================================================
￼ +
￼ +class <%= name %> < ApplicationRecord
￼ +  # ---------
￼ +  # CONSTANTS
￼ +
￼ +  # -------------
￼ +  # RELATIONSHIPS
￼ +  # -------------
￼ +
￼ +  # ------------
￼ +  # MONEY FIELDS
￼ +  # ------------
￼ +
￼ +  # ---------
￼ +  # CALLBACKS
￼ +  # ---------
￼ +
￼ +  # -----------
￼ +  # VALIDATIONS
￼ +  # -----------
￼ +
￼ +  # -------
￼ +  # METHODS
￼ +  # -------
￼ +end


11  lib/generators/bigco_model_generator.rb
+class BigcoModelGenerator < Rails::Generators::NamedBase
￼ +  desc "Generate a Bigco ActiveRecord model in app/models/"
￼ +
￼ +  argument :name, :type => :string, :required => true, :banner => 'ModelName'
￼ +
￼ +  source_root File.expand_path("../bigco/templates", __FILE__)
￼ +
￼ +  def copy_files
￼ +    template "active_record_model.rb", "app/models/#{file_name}.rb"
￼ +  end
￼ +end


Usage: rails g bigco_model FooBar


standalone factory sequence
    email = FactoryBot::Sequence.new(:email, 1) { |n| "invoice-user#{n}@claimable.com" }


    http://sunitparekh.github.io/data-anonymization/
    idea blog pose
