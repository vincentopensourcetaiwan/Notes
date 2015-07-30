# Chapter 2 Setting up RSpec

## Gemfile

	group :development, :test do
	  gem "rspec-rails", "~> 2.14.0"
	  gem "factory_girl_rails", "~> 4.2.1"
	end

	group :test do
	  gem "faker", "~> 1.1.2"
	  gem "capybara", "~> 2.1.0"
	  gem "database_cleaner", "~> 1.0.1"
	  gem "launchy", "~> 2.3.0"
	  gem "selenium-webdriver", "~> 2.33.0"
	end

## database.yml

	test:
	  adapter: mysql2
	  encoding: utf8
	  database: test
	  pool: 5
	  username: root
	  password: "123456"
	  socket: /tmp/mysql.sock

## create test database

    bundle exec rake db:create:all

## install rspec

    bundle exec rails generate rspec:install

## .rspec

    --color
    --format documentation
    --drb

## config/application.rb

    config.generators do |g|
      g.test_framework :rspec,
                       fixtures: true,
                       view_specs: false,
                       helper_specs: false,
                       routing_specs: false,
                       controller_specs: true,
                       request_specs: false
      g.fixture_replacement :factory_girl, dir: "spec/factories"
    end

## synchronize test database

    rake db:test:clone

##  / lib / tasks / dev.rake

    namespace :dev do
      desc "Rebuild system"
      task :build => ["tmp:clear", "log:clear", "db:drop", "db:create", "db:migrate", "db:seed", "db:test:clone"]
    end

# Chapter 3 Model specs

## code example

[everydayrails/rspec_rails_4](https://github.com/everydayrails/rspec_rails_4 "everydayrails/rspec_rails_4")

    git clone git@github.com:everydayrails/rspec_rails_4.git
    git checkout -b 03_models origin/03_models

# Chapter 4 Generating test data with factories

## code example

[everydayrails/rspec_rails_4](https://github.com/everydayrails/rspec_rails_4 "everydayrails/rspec_rails_4")

    git clone git@github.com:everydayrails/rspec_rails_4.git
    git checkout -b 04_factories origin/04_factories

## use gems

### use factory girl for factory

[factory_girl](https://github.com/thoughtbot/factory_girl "factory_girl")

[Get your callbacks on with Factory Girl 3.3](http://robots.thoughtbot.com/post/23039827914/get-your-callbacks-on-with-factory-girl-3-3 "Get your callbacks on with Factory Girl 3.3")

[Getting Started](https://github.com/thoughtbot/factory_girl/blob/master/GETTING_STARTED.md "Getting Started")

### fake data

**use faker**

[faker](http://faker.rubyforge.org/ "faker")

[faker document](http://rubydoc.info/gems/faker/1.0.1/frames "faker document")

**also can use forgery**

[forgery](https://github.com/sevenwire/forgery "forgery")

## spec/spec_helper.rb

    # Include Factory Girl syntax to simplify calls to factories
    config.include FactoryGirl::Syntax::Methods

## useful articles to read

[Obfuscating data in Rails applications for screenshots and demonstrations](http://everydayrails.com/2013/05/20/obfuscated-data-screenshots.html "Obfuscating data in Rails applications for screenshots and demonstrations")

# Chapter 5 Basic controller specs

## code example

[everydayrails/rspec_rails_4](https://github.com/everydayrails/rspec_rails_4 "everydayrails/rspec_rails_4")

    git clone git@github.com:everydayrails/rspec_rails_4.git
    git checkout -b 05_controller_basics origin/05_controller_basics

## useful articles to read

[custom strategies and custom callbacks](https://github.com/thoughtbot/factory_girl/blob/master/GETTING_STARTED.md#custom-strategies "custom strategies and custom callbacks")

# Chapter 6 Advanced controller specs

## code example

[everydayrails/rspec_rails_4](https://github.com/everydayrails/rspec_rails_4 "everydayrails/rspec_rails_4")

    git clone git@github.com:everydayrails/rspec_rails_4.git
    git checkout -b 06_advanced_controllers origin/06_advanced_controllers

# Chapter 7 Controller spec cleanup

## code example

[everydayrails/rspec_rails_4](https://github.com/everydayrails/rspec_rails_4 "everydayrails/rspec_rails_4")

    git clone git@github.com:everydayrails/rspec_rails_4.git
    git checkout -b 07_controller_cleanup origin/07_controller_cleanup

## creating helper macros

### spec/support/login_macros.rb

    module LoginMacros
      def set_user_session(user)
        session[:user_id] = user.id
      end
    end

### spec/spec_helper.rb

    Dir[Rails.root.join("spec/support/**/*.rb")].each { |f| require f }

    RSpec.configure do |config|
      config.include LoginMacros
    end

## using custom RSpec matchers

You can store custom matchers in spec/support/matchers, one matcher per file, the default RSpec configuration will automatically pick them up for use in your specs.

## useful articles to read

[Custom Matchers](https://github.com/dchelimsky/rspec/wiki/Custom-Matchers "Custom Matchers")

# Chapter 8 Feature specs

## code example

[everydayrails/rspec_rails_4](https://github.com/everydayrails/rspec_rails_4 "everydayrails/rspec_rails_4")

    git clone git@github.com:everydayrails/rspec_rails_4.git
    git checkout -b 08_features origin/08_features

## references

[capybara](https://github.com/jnicklas/capybara "capybara")

[database_cleaner](https://github.com/bmabey/database_cleaner "database_cleaner")

[launchy](http://rubygems.org/gems/launchy "launchy")

## spec/features/*_spec.rb

    require'spec_helper'

    feature'myfeature'do
        background do
            # add setup details
        end

        scenario 'my first test' do
            # write the example!
        end
    end

## debugging feature specs

use Launchy to saves the feature spec’s current HTML to a temporary file and renders it in your default browser.

to use Launchy in a spec, add the following line anywhere you’d like to see the results of the previous step:

    save_and_open_page

## a little refactoring

### spec/support/login_macros.rb

    module LoginMacros
       # controller login helper omitted ...
       def sign_in(user)
           visit root_path
           click_link 'Log In'
           fill_in 'Email', with: user.email fill_in 'Password', with: user.password click_button 'Log In'
       end
    end

### spec/features/users_spec.rb

    feature'Usermanagement'do
        scenario "adds a new user" do
            admin = create(:admin)
            sign_in admin
            # remaining steps omitted ...
        end
    end

## including JavaScript interactions

use Selenium to interacte with JavaScript, through your computer’s installation of Firefox.

**added js: true to the scenario**

spec/features/aobut_us_spec.rb

    require'spec_helper'
    feature "AboutBigComodal" do
        scenario "toggles display of the modal about display", js: true do
            # the example ...
        end
    end

## configure Database Cleaner to help with database transactions

### spec/spec_helper.rb

    RSpec.configuredo|config|
        # earlier configurations omitted ...
        # Set config.use_transactional_fixtures to false
        config.use_transactional_fixtures = false
        config.before(:suite) do
            DatabaseCleaner.strategy = :truncation
        end
        config.before(:each) do
            DatabaseCleaner.start
        end
        config.after(:each) do DatabaseCleaner.clean
    end

### spec/support/shared_db_connection.db

    class ActiveRecord::Base
      mattr_accessor :shared_connection
      @@shared_connection = nil

      def self.connection
        @@shared_connection || retrieve_connection
      end
    end
    ActiveRecord::Base.shared_connection = ActiveRecord::Base.connection

## useful articles to read

[configuring database_cleaner with rails rspec capybara and selenium](http://devblog.avdi.org/2012/08/31/configuring-database_cleaner-with-rails-rspec-capybara-and-selenium/ "configuring database_cleaner with rails rspec capybara and selenium")

## capybara drivers

### two headless options supporting JavaScript for Capybara

[capybara-webkit ](https://github.com/thoughtbot/capybara-webkit "capybara-webkit ")

[poltergeist](https://github.com/jonleighton/poltergeist "poltergeist")


### setup up capybara drivers

[capybara drivers](https://github.com/jnicklas/capybara#drivers "capybara drivers")

# Chapter 9 Speeding up specs

## code example

[everydayrails/rspec_rails_4](https://github.com/everydayrails/rspec_rails_4 "everydayrails/rspec_rails_4")

    git clone git@github.com:everydayrails/rspec_rails_4.git
    git checkout -b 09_speedup origin/09_speedup

##  nice gem to improve writing codes speed

[Shoulda](http://rubygems.org/gems/shoulda "Shoulda")

## mocks library

[RSpec Mocks](http://rubydoc.info/gems/rspec-mocks/frames "RSpec Mocks")

[mocha](http://rubygems.org/gems/mocha "mocha")

[other options](https://www.ruby-toolbox.com/categories/mocking "other options")

## nice courses

[peepcode rspec](https://peepcode.com/screencasts/rspec "peepcode rspec")

##  guard

[guard](https://github.com/guard/guard "guard")

Gemfile

    group :development do
      gem 'guard-rspec', '~> 3.0.2'
      gem 'guard-spork', '~> 1.5.1'
      gem 'spork-rails', github: 'sporkrb/spork-rails'
      gem 'rb-fsevent', '~> 0.9.3'
    end

create a Guardfile

    bundle exec guard init rspec

Guardfile

    guard 'spork', :cucumber_env => { 'RAILS_ENV' => 'test' }, :rspec_env => { 'RAILS_ENV' => 'test' } do
      watch('config/routes.rb')
      watch('config/application.rb')
      watch('config/environment.rb')
      watch('config/environments/test.rb')
      watch(%r{^config/initializers/.+\.rb$})
      watch('Gemfile')
      watch('Gemfile.lock')
      watch('spec/spec_helper.rb') { :rspec }
    end

    guard 'rspec', notification: false, all_on_start: false, all_on_pass: false, cli: "--drb" do
      watch(%r{^spec/.+_spec\.rb$})
      watch(%r{^lib/(.+)\.rb$})     { |m| "spec/lib/#{m[1]}_spec.rb" }
      watch('spec/spec_helper.rb')  { "spec" }

      watch(%r{^app/(.+)\.rb$})                           { |m| "spec/#{m[1]}_spec.rb" }
      watch(%r{^app/(.*)(\.erb|\.haml)$})                 { |m| "spec/#{m[1]}#{m[2]}_spec.rb" }
      watch(%r{^app/controllers/(.+)_(controller)\.rb$})  { |m| "spec/#{m[2]}s/#{m[1]}_#{m[2]}_spec.rb" }
      watch(%r{^spec/support/(.+)\.rb$})                  { "spec" }
      watch('app/controllers/application_controller.rb')  { "spec/controllers" }

      watch(%r{^app/views/(.+)/.*\.(erb|haml)$})          { |m| "spec/features/#{m[1]}_spec.rb" }
    end

options suggestion

    notification: false
    all_on_start: false
     all_on_pass: false

start guard

    bundle exec guard

### only run the full test suite on demand

Guardfile

    guard'rspec',version:2,cli:'--color--formatdocumentation',
    all_on_start: false, all_after_pass: false do

[full list of guard](https://github.com/guard "full list of guard")

[more on guard](http://railscasts.com/episodes/264-guard "more on guard")

## speed up testing by preload rails with Spork

[#285 Spork](http://railscasts.com/episodes/285-spork "#285 Spork")

### Spork configuration

Guardfile

    guard 'spork', :cucumber_env => { 'RAILS_ENV' => 'test' }, :rspec_env => { 'RAILS_ENV' => 'test' } do
      watch('config/routes.rb')
      watch('config/application.rb')
      watch('config/environment.rb')
      watch('config/environments/test.rb')
      watch(%r{^config/initializers/.+\.rb$})
      watch('Gemfile')
      watch('Gemfile.lock')
      watch('spec/spec_helper.rb') { :rspec }
    end

    guard 'rspec', notification: false, all_on_start: false, all_on_pass: false, cli: "--drb" do
      watch(%r{^spec/.+_spec\.rb$})
      watch(%r{^lib/(.+)\.rb$})     { |m| "spec/lib/#{m[1]}_spec.rb" }
      watch('spec/spec_helper.rb')  { "spec" }

      watch(%r{^app/(.+)\.rb$})                           { |m| "spec/#{m[1]}_spec.rb" }
      watch(%r{^app/(.*)(\.erb|\.haml)$})                 { |m| "spec/#{m[1]}#{m[2]}_spec.rb" }
      watch(%r{^app/controllers/(.+)_(controller)\.rb$})  { |m| "spec/#{m[2]}s/#{m[1]}_#{m[2]}_spec.rb" }
      watch(%r{^spec/support/(.+)\.rb$})                  { "spec" }
      watch('app/controllers/application_controller.rb')  { "spec/controllers" }

      watch(%r{^app/views/(.+)/.*\.(erb|haml)$})          { |m| "spec/features/#{m[1]}_spec.rb" }
    end

spec/spec_helper.rb

    require 'rubygems'
    require 'spork'
    require 'database_cleaner'

    Spork.prefork do
      ENV["RAILS_ENV"] ||= 'test'
      require File.expand_path("../../config/environment", __FILE__)
      require 'rspec/rails'

      RSpec.configure do |config|
        config.use_transactional_fixtures = false
        config.infer_base_class_for_anonymous_controllers = false
        config.order = "random"

        config.before :each do
          if Capybara.current_driver == :selenium
            DatabaseCleaner.strategy = :truncation
          else
            DatabaseCleaner.strategy = :transaction
          end
          DatabaseCleaner.start
        end

        config.after(:each) do
          DatabaseCleaner.clean
        end

        # Include Factory Girl syntax to simplify calls to factories
        config.include FactoryGirl::Syntax::Methods
      end
    end

    Spork.each_run do
      Dir[Rails.root.join("spec/support/**/*.rb")].each {|f| require f}
      FactoryGirl.reload
      include LoginMacros
    end

[spork-rails](https://github.com/sporkrb/spork-rails "spork-rails")

### spork alternative

[zeus](https://github.com/burke/zeus "zeus")

[commands](https://github.com/rails/commands "commands")

[spring](https://github.com/jonleighton/spring "spring")

## tags

[RSpec’s tags feature](https://www.relishapp.com/rspec/rspec-core/v/2-4/docs/command-line/tag-option "RSpec’s tags feature")

**example**

    it "processes a credit card", focus: true do # details of example
    end

run only the specs with the focus tag

    bundle exec rspec . --tag focus

**configure RSpec to only run (or never run) examples with specific tags; for example:**

spec/spec_helper.rb

    RSpec.configure do |c|
        c.filter_run focus: true
        c.filter_run_excluding slow: true
    end

## Other speedy solutions

**mark it as a pending spec**

    it "loads a lot of data" do
        pending "no longer necessary"
        # your spec's code; it will not be run
    end

**Take Rails out of the equation**

[Corey Haines’ talk on the subject](http://confreaks.com/videos/641-gogaruco2011-fast-rails-tests "Corey Haines’ talk on the subject")

[Destroy All Software](http://confreaks.com/videos/641-gogaruco2011-fast-rails-tests "Destroy All Software")


# Chapter 10 Testing the rest

## Testing email delivery

### use  email-spec gem

[email_spec](http://rubygems.org/gems/email_spec "email_spec")

[documentation](http://rubydoc.info/gems/email_spec/1.5.0/frames "documentation")

Gemfile

    group :test do
      gem 'email_spec'
    end

spec/spec_helper.rb

    require"email_spec"
    config.include(EmailSpec::Helpers)
    config.include(EmailSpec::Matchers)

code examples

    expect(open_last_email).to be_delivered_from sender.email
    expect(open_last_email).to have_reply_to sender.email
    expect(open_last_email).to be_delivered_to recipient.email
    expect(open_last_email).to have_subject message.subject
    expect(open_last_email).to have_body_text message.message

send email example

    email = MessageMailer.create_friend_request("aaron@everydayrails.com")
    expect(email).to deliver_to("aaron@everydayrails.com")
    expect(email).to have_subject "Friend Request"

good article to read

[#275 How I Test](http://railscasts.com/episodes/275-how-i-test "#275 How I Test")

## Testing file uploads

Place a small, dummy file in spec/factories directory. Then you can refer to it in a factory like so:

    FactoryGirl.define do
        factory :user do
            sequence(:username) { |n| "user#{n}"}
            password 'secret'
            avatar { File.new("#{Rails.root}/spec/factories/avatar.png") }
        end
    end

you can also access the file explicitly in specs, such as the following request example:

    it "creates a new user " do
        visit new_user_url
        fill_in 'Username', with: 'aaron'
        fill_in 'Password', with: 'secret'
        attach_file 'Avatar',
            File.new("#{Rails.root}/spec/factories/avatar.png")
        click_button 'Sign up'
        expect(User.last.avatar_file_name).to eq 'avatar.png'
    end

Using the factory above, we can also test this at the controller level like this:

    it "uploads an avatar" do
        post :create, user: create(:user)
        expect(assigns(:user).avatar_file_name).to eq 'avatar.png'
    end

[paperclip testing](https://github.com/thoughtbot/paperclip#testing "paperclip testing")

## Testing the time

### use timecop gem

[timecop](http://rubygems.org/gems/timecop "timecop")

**test a special day**

    it "wishes the visitor a Happy New Year on January 1"do
        Timecop.travel Time.parse("January 1")
        visit root_url
        expect(page).to have_content "Happy New Year!"
        Timecop.return
    end

Take note of the call to Timecop.return in these code samples. This resets the clock to your system’s time and helps RSpec properly report the amount of time your tests take to run.

**impose a deadline**

    it "doesn't allow taxpayers to file after April 15"do
        Timecop.travel Time.parse("April 16")
        visit tax_submit_form
        expect(page).to have_content "Sorry, you're too late!"
        Timecop.return
    end


    it "gives taxpayers up until the 15th to file"do
        Timecop.travel Time.parse("April 15")
        visit tax_submit_form
        expect(page).to have_content "There's still time to file, but hurry!"
        Timecop.return
    end

**stop time during the test**

    it "stamps the model's created at with the current time" do
        Timecop.freeze
        user = create(:user)
        expect(user.created_at).to eq Time.now
        Timecop.return
    end

## Testing web services

[#276 Testing Time & Web Requests](http://railscasts.com/episodes/276-testing-time-web-requests "#276 Testing Time & Web Requests")

[#291 Testing with VCR pro](http://railscasts.com/episodes/291-testing-with-vcr "#291 Testing with VCR pro")

## Testing rake tasks

abstract any code you’ve got in a given Rake task into a class or module, then call that method within the task.

original Rake

    namespace :legacy do
        desc "Move Persons to Contacts"
        task person: :environment do
            Person.all.each do |person|
                Contact.create!(
                    firstname: person.firstname,
                    lastname:  person.lastname,
                    email:     person.email
                    )
            end
        end
    end

create a Legacy class lib/legacy.rb and move the bulk of the task to a class method within it:

lib/legacy.rb

    class Legacy
        def self.move_people
            Person.all.each do |person|
                contact.create!(
                    firstname: person.firstname,
                    lastname:  person.lastname,
                    email:     person.email
                )
            end
        end
    end

update my original Rake task:

    namespace :legacy do
        desc "Move Persons to Contacts"
            task person: :environment do
                Legacy.move_people
            end
    end

testing the rake

spec/lib/legacy_spec.rb

    require 'spec_helper'

    describe Legacy do
        it 'creates a contact from a person'
        #etc.
    end

# Chapter 11 Toward test-driven development

## code example

[everydayrails/rspec_rails_4](https://github.com/everydayrails/rspec_rails_4 "everydayrails/rspec_rails_4")

    git clone git@github.com:everydayrails/rspec_rails_4.git
    git checkout -b 11_tdd origin/11_tdd



























































































