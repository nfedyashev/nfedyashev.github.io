namespace :db do
  desc "Dumps the database to db/APP_NAME.dump"
  task dump: :environment do
    raise("Could only be executed in development environment") unless Rails.env.development?

    cmd = nil
    with_config do |app, host, db, user|
      cmd = "pg_dump --host #{host} --username #{user} -Fp --verbose --clean --no-owner --no-acl --format=c #{db} > #{Rails.root}/db/#{app}.dump"
    end
    puts cmd
    exec cmd
  end
  
  private

  def with_config
    yield Rails.application.class.parent_name.underscore,
     'localhost',
      ActiveRecord::Base.connection_config[:database],
      #TODO
      'nikita'
  end
end
