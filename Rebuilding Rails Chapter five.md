# Rebuilding Rails Chapter 5

## Fork examples

https://github.com/noahgibbs/rulers

https://github.com/noahgibbs/best_quotes

## Checkout chapter 5 branch
```
git checkout -b chapter_5_mine chapter_5
```

## Add “multi_json” dependency 
**rulers/rulers.gemspec** 
```ruby
gem.add_runtime_dependency "multi_json"
```
```
bundle install
```

## Create a JSON object
**best_quotes/db/quotes/1.json**
```json
{
  "submitter": "Jeff",
  "quote": "A penny saved is a penny earned.",
  "attribution": "Ben Franklin"
}
```

## Add file_model object
**rulers/lib/rulers.rb**
```ruby
require "rulers/file_model"
```
**rulers/lib/rulers/file_model.rb**
```ruby
require "multi_json"
module Rulers
  module Model
    class FileModel
      def initialize(filename)
        @filename = filename
        # If filename is "dir/37.json", @id is 37
        basename = File.split(filename)[-1]
        @id = File.basename(basename, ".json").to_i
        obj = File.read(filename)
        @hash = MultiJson.load(obj)
      end

      def [](name)
        @hash[name.to_s]
      end

      def []=(name, value)
        @hash[name.to_s] = value
      end

      def self.find(id)
        begin
          FileModel.new("db/quotes/#{id}.json")
        rescue
          return nil
        end
      end
    end
  end
end
```

## Show the quote
**best_quotes/app/controllers/quotes_controller.rb**
```ruby
def quote_1
  quote_1 = Rulers::Model::FileModel.find(1)
  render :quote, :obj => quote_1
end
```
**best_quotes/app/views/quotes/quote.html.erb**
```html
<p>
  &quot;<%= obj["quote"] %>&quot;
  <br/> &ndash; <%= obj["attribution"] %>
</p>
<p>
  Submitted by <%= obj["submitter"] %>
</p>
```

## The monent of truth
**best_quotes/**
```
bundle exec rackup -p 3001
```
http://localhost:3001/quotes/quote_1

## Inclusion and Convenience
**rulers/lib/rulers/controller.rb**
```ruby
require "rulers/file_model"

module Rulers
  class Controller
    include Rulers::Model
  end
end

```

## Queries
**rulers/lib/rulers/file_model.rb**
```ruby
def self.all
  files = Dir["db/quotes/*.json"]
  files.map { |f| FileModel.new f }
end
```
**best_quotes/app/controllers/quotes_controller.rb**
```ruby
def index
  quotes = FileModel.all
  render :index, :quotes => quotes
end
```
**best_quotes/app/views/quotes/index.html.erb**
```html
<% quotes.each do |q| %>
    <p> <%= q["quote"] %> </p>
<% end %>
```

## Create method
**rulers/lib/rulers/file_model.rb**
```ruby
      def self.create(attrs)
        hash = {}
        hash["submitter"] = attrs["submitter"] || ""
        hash["quote"] = attrs["quote"] || ""
        hash["attribution"] = attrs["attribution"] || ""

        files = Dir["db/quotes/*.json"]
        names = files.map { |f| f.split("/")[-1] }
        highest = names.map { |b| b[0...-5].to_i }.max
        id = highest + 1

        File.open("db/quotes/#{id}.json", "w") do |f|
          f.write <<TEMPLATE
{
  "submitter": "#{hash["submitter"]}",
  "quote": "#{hash["quote"]}",
  "attribution": "#{hash["attribution"]}"
}
TEMPLATE
        end

        FileModel.new "db/quotes/#{id}.json"
      end

```
**best_quotes/app/controllers/quotes_controller.rb**
```ruby
def new_quote
  attrs = {
    "submitter" => "web user",
    "quote" => "A picture is worth one k pixels",
    "attribution" => "Me"
  }
  m = FileModel.create attrs
  render :quote, :obj => m
end
```

## Exercises
**Edit and Update method**

**Caching and Sharing Models**

**Add new methods**
```ruby
find_all_by_submitter()
method_missing()
```

## Rails
http://yehudakatz.com/2010/01/10/activemodel-make-any-ruby-object-feel-like-activerecord/





