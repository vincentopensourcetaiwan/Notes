### reference:

[Facebook Authentication](https://github.com/plataformatec/devise/wiki/OmniAuth%3a-Overview "Facebook Authentication")


### example project

[TheFABULABS/shopify](https://github.com/TheFABULABS/shopify "TheFABULABS/shopify")

### register facebook apps

[register google app](https://code.google.com/apis/console/ "register google apps")

### Redirect's URIs

    http://localhost:3000/users/auth/google_oauth2/callback

### Gemfile

    gem 'omniauth'
    gem 'omniauth-google-oauth2'

### add columns to user

    rails g migration AddColumnsToUsers provider uid

### app/models/user.rb

    attr_accessible :provider, :uid

### config/initializers/devise.rb

    require "omniauth-google-oauth2"
    config.omniauth :google_oauth2, "APP_ID", "APP_SECRET", { access_type: "offline", approval_prompt: "" }

### app/models/user.rb

    devise :omniauthable

### app/views/layouts/_navigation.html.erb

    <%= link_to "Sign in with Google", user_omniauth_authorize_path(:google_oauth2) %>

### config/routes.rb

    devise_for :users, :controllers => { :omniauth_callbacks => "users/omniauth_callbacks" }

### app/controllers/users/omniauth_callbacks_controller.rb

    def google_oauth2
          # You need to implement the method below in your model (e.g. app/models/user.rb)
          @user = User.find_for_google_oauth2(request.env["omniauth.auth"], current_user)

          if @user.persisted?
            flash[:notice] = I18n.t "devise.omniauth_callbacks.success", :kind => "Google"
            sign_in_and_redirect @user, :event => :authentication
          else
            session["devise.google_data"] = request.env["omniauth.auth"]
            redirect_to new_user_registration_url
          end
      end

### app/models/user.rb

    def self.find_for_google_oauth2(access_token, signed_in_resource=nil)
      data = access_token.info
      user = User.where(:email => data["email"]).first

      unless user
        user = User.create(name: data["name"],
                           email: data["email"],
                           password: Devise.friendly_token[0, 20]
        )
      end
      user
    end

### skip confirmation

    def self.find_for_google_oauth2(access_token, signed_in_resource=nil)
      data = access_token.info
      user = User.where(:email => data["email"]).first

      unless user
        user = User.create(name: data["name"],
                           email: data["email"],
                           password: Devise.friendly_token[0, 20]
        )
        user.skip_confirmation!
      end
      user
    end

