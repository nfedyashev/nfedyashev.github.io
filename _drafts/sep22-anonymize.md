[see how to do heroku db dump](posts/sep22-how-to)

```
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
