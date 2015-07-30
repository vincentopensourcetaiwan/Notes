# error

> An error occured while installing pg

    sudo apt-get install libpq-dev

# error

> [paperclip] An error was received while processing: #<Paperclip::CommandNotFoundError: Could not run the `identify` command. Please install ImageMagick.

    sudo apt-get install imagemagick

# error

> ** [out :: 192.168.0.118] Gem::Installer::ExtensionBuildError: ERROR: Failed to build gem native extension.

> ** [out :: 192.168.0.118]

> ** [out :: 192.168.0.118] /usr/local/bin/ruby extconf.rb

> ** [out :: 192.168.0.118] checking for libxml/parser.h... no

> ** [out :: 192.168.0.118] -----

> ** [out :: 192.168.0.118] libxml2 is missing.  please visit http://nokogiri.org/tutorials/installing_nokogiri.html for help with installing dependencies.

    # ruby developer packages
	sudo apt-get install ruby1.8-dev ruby1.8 ri1.8 rdoc1.8 irb1.8
	sudo apt-get install libreadline-ruby1.8 libruby1.8 libopenssl-ruby
	# nokogiri requirements
	sudo apt-get install libxslt-dev libxml2-dev
	sudo gem install nokogiri

# error

> No route matches “/users/sign_out” devise rails 3

    you must include <%= javascript_include_tag :application %> in your layout file
    application.html.erb

# error

> Ajax Delete links log out current_user

     include a call to the csrf_meta_tag helper in layout's head

[Ajax Delete links log out current_user](http://stackoverflow.com/questions/6434283/rails-3-ajax-delete-links-log-out-current-user "Ajax Delete links log out current_user")

> devise can not logout

include following line in layout's head

    <%= javascript_include_tag "application" %>

[devise can not logout](http://stackoverflow.com/questions/6557311/no-route-matches-users-sign-out-devise-rails-3 "devise can not logout")




