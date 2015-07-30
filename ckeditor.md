## References

[galetahub/ckeditor](https://github.com/galetahub/ckeditor "galetahub/ckeditor")

## Installation

gemfile

    gem "ckeditor"

## generate models for store uploading files

gemfile

    gem "paperclip"

console

    rails generate ckeditor:install --orm=active_record --backend=paperclip

config/application.rb

    config.autoload_paths += %W(#{config.root}/app/models/ckeditor)

config/routes.rb

    mount Ckeditor::Engine => "/ckeditor"

## Usage

application.js

    //= require ckeditor/init

Form helpers:

    <%= f.input :content, :as => :ckeditor, :input_html => {:ckeditor => {:toolbar => 'Full'}} %>

## create folder for ckeditor on production server

    sudo su deploy
    mkdir /home/deploy/thefabulabs-shopify/shared/ckeditor_assets

## .gitignore

    /public/ckeditor_assets

**delete all the in the /public/ckeditor_assets, and git commit**


## setup link to the folder

config/deploy/production.rb

    namespace :deploy do
      desc "restart"
      task :restart do
        run "touch #{current_path}/tmp/restart.txt"
        run "ln -nsf #{shared_path}/ckeditor_assets #{current_path}/public/ckeditor_assets"
      end
    end

or

config/deploy/production.rb

    namespace :deploy do
      %w[start stop restart].each do |command|
        desc "#{command} unicorn server"
        task command, roles: :app, except: {no_release: true} do
          run "/etc/init.d/unicorn_#{application} #{command}"
          run "ln -nsf #{shared_path}/ckeditor_assets #{current_path}/public/ckeditor_assets"
        end
      end
    end



