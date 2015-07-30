## References

[ #164 Cron in Ruby (revised)](http://railscasts.com/episodes/164-cron-in-ruby-revised " #164 Cron in Ruby (revised)")

[javan/whenever](https://github.com/javan/whenever "javan/whenever")

[comments from #164 Cron in Ruby (revised)](http://railscasts.com/episodes/164-cron-in-ruby-revised?view=comments  "comments from #164 Cron in Ruby (revised)")

[Rails 3.2: Trying to use 'Whenever' gem but log file keeps giving: Could not find multi_json-1.1.0 in any of the sources (Bundler::GemNotFound) error](http://stackoverflow.com/questions/11038403/rails-3-2-trying-to-use-whenever-gem-but-log-file-keeps-giving-could-not-fin "Rails 3.2: Trying to use 'Whenever' gem but log file keeps giving: Could not find multi_json-1.1.0 in any of the sources (Bundler::GemNotFound) error")

## demo project

[demo project](https://bitbucket.org/vlin/test-whenever "demo project")

## Installation

Gemfile

    gem 'whenever', :require => false
    
## Getting started

    wheneverize .
    
## setup schedule

config/schedule.rb

    env :PATH, ENV['PATH']

    set :output, "#{path}/log/cron.log"

    every 1.minute do
        rake "dev:test"
    end

## Capistrano integration

config/deploy.rb

    set :whenever_command, "bundle exec whenever"
    require "whenever/capistrano"


## error messages

**does not work on development environment**

    bundle exec whenever --set 'environment=development' -w

[comments from #164 Cron in Ruby (revised)](http://railscasts.com/episodes/164-cron-in-ruby-revised?view=comments  "comments from #164 Cron in Ruby (revised)")

**/bin/bash bundle command not found**

schedule.rb

    env :PATH, ENV['PATH']



    
