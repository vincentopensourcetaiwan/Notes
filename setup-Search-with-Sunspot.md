**test project:**

[test sunspot](https://bitbucket.org/vlin/test-sunspot "test sunspot")

**references:**

[Sunspot](https://github.com/sunspot/sunspot "Sunspot")

[Search with Sunspot](http://railscasts.com/episodes/278-search-with-sunspot "Search with Sunspot")

**scaffold Article**

    rails g scaffold Article name:string content:text
    
**add gem**

Gemfile

    gem 'sunspot_rails'
    gem 'sunspot_solr'
    
**Generate a default configuration file:**

    rails generate sunspot_rails:install
    
**start the packaged Solr distribution with**

    bundle exec rake sunspot:solr:start
    
**setup sunspot**

models/article.rb

    searchable do
        text :name, :content
    end

articles_controller.rb

    def index
        @search = Article.search do
            fulltext params[:search]
        end
        @articles = @search.results
    end
    
articles/index.html.erb

    <%= form_tag articles_path, :method => :get do %>
        <p>
            <%= text_field_tag :search, params[:search] %>
            <%= submit_tag "Search", :name => nil %>
        </p>
    <% end %>
    
**install Java runtime on ubuntu**

    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer
    
[Java Runtime error when trying to install sunspot](http://stackoverflow.com/questions/9976626/java-runtime-error-when-trying-to-install-sunspot "Java Runtime error when trying to install sunspot")

**setup with whenever**

config/schedule.rb

    every :reboot do
        command "cd /home/deployer/apps/brandinlabs/current/ && RAILS_ENV=production bundle exec sunspot-solr start --port=8983 --data-directory=/home/deployer/apps/brandinlabs/shared/solr/data --pid-dir=/home/deployer/apps/brandinlabs/shared/pids"
    end

**reindex at prodution server**

    sudo su deployer
    cd /home/deployer/apps/labsunion/current
    bundle exec rake sunspot:solr:reindex RAILS_ENV=production


