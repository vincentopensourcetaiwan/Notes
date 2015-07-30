### reference:

[Facebook Authentication](https://github.com/plataformatec/devise/wiki/OmniAuth%3a-Overview "Facebook Authentication")


### example project

[TheFABULABS/shopify](https://github.com/TheFABULABS/shopify "TheFABULABS/shopify")

### register facebook apps

[register facebook apps](https://developers.facebook.com/apps "register facebook apps")

### Gemfile

    gem 'omniauth'
    gem 'omniauth-facebook'

### add columns to user

    rails g migration AddColumnsToUsers provider uid

### app/models/user.rb

    attr_accessible :provider, :uid

### config/initializers/devise.rb

    require "omniauth-facebook"
    config.omniauth :facebook, "345461325544566", "63e0fd63cd1e559b812c024bb6c4c31d"

### app/models/user.rb

    devise :omniauthable, :omniauth_providers => [:facebook]

### app/views/layouts/_navigation.html.erb

    <%= link_to "Sign in with Facebook", user_omniauth_authorize_path(:facebook) %>

### config/routes.rb

    devise_for :users, :controllers => { :omniauth_callbacks => "users/omniauth_callbacks" }

### app/controllers/users/omniauth_callbacks_controller.rb

    class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
      def facebook
        # You need to implement the method below in your model (e.g. app/models/user.rb)
        @user = User.find_for_facebook_oauth(request.env["omniauth.auth"], current_user)

        if @user.persisted?
          sign_in_and_redirect @user, :event => :authentication #this will throw if @user is not activated
          set_flash_message(:notice, :success, :kind => "Facebook") if is_navigational_format?
        else
          session["devise.facebook_data"] = request.env["omniauth.auth"]
          redirect_to new_user_registration_url
        end
      end
    end

### app/models/user.rb

    def self.find_for_facebook_oauth(auth, signed_in_resource=nil)
      user = User.where(:provider => auth.provider, :uid => auth.uid).first
      unless user
        user = User.create(name: auth.extra.raw_info.name,
                           provider: auth.provider,
                           uid: auth.uid,
                           email: auth.info.email,
                           password: Devise.friendly_token[0, 20]
        )
      end
      user
    end

### skip confirmation

    def self.find_for_facebook_oauth(auth, signed_in_resource=nil)
      user = User.where(:provider => auth.provider, :uid => auth.uid).first
      unless user
        user = User.create(name: auth.extra.raw_info.name,
                           provider: auth.provider,
                           uid: auth.uid,
                           email: auth.info.email,
                           password: Devise.friendly_token[0, 20]
        )
        user.skip_confirmation!
      end
      user
    end

