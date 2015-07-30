### Gemfile

    gem "paperclip"
    gem 'aws-sdk'

### /config/environment.rb

    require "#{File.dirname(__FILE__)}/../config/environment.rb"

### /config/s3.yml

    development:
      bucket: s3-heroku
      access_key_id: AKIAJR2Q7DOLSFPQABAA
      secret_access_key: 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

    test:
      bucket: s3-heroku
      access_key_id: AKIAJR2Q7DOLSFPQABAA
      secret_access_key: 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

    production:
      bucket: s3-heroku
      access_key_id: AKIAJR2Q7DOLSFPQABAA
      secret_access_key: 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

### /config/initializers/aws.rb

    Paperclip.interpolates(:s3_tokyo_url) { |attachment, style|
      "#{attachment.s3_protocol}://s3-ap-northeast-1.amazonaws.com/#{attachment.bucket_name}/#{attachment.path(style).gsub(%r{^/}, "")}"
    }

### /app/models/friend.rb

    attr_accessible :name, :avatar

    has_attached_file :avatar, styles: {
      thumb:  '100x100>',
      square: '200x200#',
      medium: '300x300>'
    },
                      :storage         => :s3,
                      :s3_credentials  => "#{Rails.root}/config/s3.yml",
                      :path            => ":attachment/:id/:style.:extension",
                      :url             => ":s3_tokyo_url"


### for galetahub/ckeditor gem

**/ app / models / ckeditor / attachment_file.rb**

    has_attached_file :data,
                        :storage         => :s3,
                        :s3_credentials  => "#{Rails.root}/config/s3.yml",
                        :path            => ":attachment/:id/:style.:extension",
                        :url             => ":s3_tokyo_url"
                        #:url => "/ckeditor_assets/attachments/:id/:filename",
                        #:path => ":rails_root/public/ckeditor_assets/attachments/:id/:filename"

**/ app / models / ckeditor / picture.rb**

    has_attached_file :data,
                        :storage         => :s3,
                        :s3_credentials  => "#{Rails.root}/config/s3.yml",
                        :path            => ":attachment/:id/:style.:extension",
                        :url             => ":s3_tokyo_url",
                        :styles => { :content => '800>', :thumb => '118x100#' }
                        #:url  => "/ckeditor_assets/pictures/:id/:style_:basename.:extension",
                        #:path => ":rails_root/public/ckeditor_assets/pictures/:id/:style_:basename.:extension",
                        #:styles => { :content => '800>', :thumb => '118x100#' }

### capistrano

/ config / deploy.rb

    task :setup_config, roles: :app do
      put File.read("config/s3.example.yml"), "#{shared_path}/config/s3.yml"

      puts "Now edit the config files in #{shared_path}."
    end

    after "deploy:setup", "deploy:setup_config"

    task :symlink_config, roles: :app do
      run "ln -nfs #{shared_path}/config/s3.yml #{release_path}/config/s3.yml"
    end

### error message

**please install nokogiri**

    # ruby developer packages
	sudo apt-get install ruby1.8-dev ruby1.8 ri1.8 rdoc1.8 irb1.8
	sudo apt-get install libreadline-ruby1.8 libruby1.8 libopenssl-ruby
	# nokogiri requirements
	sudo apt-get install libxslt-dev libxml2-dev
	sudo gem install nokogiri

**Please re-send this request to the specified temporary endpoint. Continue to use the original request endpoint for future requests.**

[AWS-S3-Errors-TemporaryRedirect-With-Rails-And-Paperclip](http://rawlins.weboffins.com/2013/02/06/AWS-S3-Errors-TemporaryRedirect-With-Rails-And-Paperclip/ "AWS-S3-Errors-TemporaryRedirect-With-Rails-And-Paperclip")

Give your bucket some hours or so and it'll start to work.

