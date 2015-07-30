## References

[Speed up Heroku deploys](http://blog.alexmaccaw.com/faster-deploys "Speed up Heroku deploys")

[ndbroadbent/turbo-sprockets-rails3](https://github.com/ndbroadbent/turbo-sprockets-rails3 "ndbroadbent/turbo-sprockets-rails3")

[一次搞懂 Assets Pipeline](http://gogojimmy.net/2012/07/03/understand-assets-pipline/ "一次搞懂 Assets Pipeline")

[Speeding up Capistrano Deployments from 6 Minutes to 9 Seconds by Skipping Asset Compilation/turbo-sprockets-rails3](http://theadmin.org/articles/capistrano-skipping-precompile-assets/ "Speeding up Capistrano Deployments from 6 Minutes to 9 Seconds by Skipping Asset Compilation")

[skip asset precompile completely](http://stackoverflow.com/questions/9016002/speed-up-assetsprecompile-with-rails-3-1-3-2-capistrano-deployment "skip asset precompile completely")


## Gemfile

	group :assets do
	  gem 'turbo-sprockets-rails3'
	end


## Capfile

    load 'deploy/assets'
    load 'config/deploy' # remove this line to skip loading any of the default tasks


## config/deploy.rb

    namespace :deploy do
      namespace :assets do
        task :precompile, :roles => :web, :except => {:no_release => true} do
          from = source.next_revision(current_revision)
          if capture("cd #{latest_release} && #{source.local.log(from)} vendor/assets/ app/assets/ | wc -l").to_i > 0
            run_locally "bundle exec rake assets:precompile RAILS_ENV=development"
            find_servers_for_task(current_task).each do |server|
              run_locally "rsync -vr --exclude='.DS_Store' public/assets #{user}@#{server.host}:#{shared_path}/"
            end
          else
            logger.info "Skipping asset pre-compilation because there were no asset changes"
          end
        end
      end
    end

## skip asset precompile completely

    callback = callbacks[:after].find{|c| c.source == "deploy:assets:precompile" }
    callbacks[:after].delete(callback)
    after 'deploy:update_code', 'deploy:assets:precompile' unless fetch(:skip_assets, false)

# command

    cap deploy -S skip_assets=true
