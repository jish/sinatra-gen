## sinatra-gen -- A simple Sinatra generator

I write small [Ruby][1] web services in [Sinatra][2] every once in a while. I
found myself writing the same boilerplate code over and over again. This bash
script will generate a simple Sinatra app.

[1]: https://www.ruby-lang.org/en/
[2]: https://github.com/sinatra/sinatra/

### Generating a new project

`sinatra-gen` generates a Sinatra project in the current directory.

    $ mkdir foo
    $ cd foo
    $ sinatra-gen foo

A test suite is created for you.

    $ rake test
    $ autotest

### What is generated?

When you generate a new project the following files are generated:

    $ find .
    .
    ./.autotest
    ./config.ru
    ./Gemfile
    ./Gemfile.lock
    ./lib
    ./lib/foo.rb
    ./Rakefile
    ./test
    ./test/foo_test.rb

The implementation

```ruby
# lib/foo.rb
require 'sinatra/base'

class Foo < Sinatra::Base

  get "/" do
    [200, {}, ["Hello World!"]]
  end

end
```

A test

```ruby
# test/foo_test.rb
require 'minitest/autorun'
require 'rack/test'
require 'foo'

class FooTest < MiniTest::Test

  include Rack::Test::Methods

  def app
    Foo.new
  end

  def test_root_route
    get "/"
    assert last_response.ok?, "Must respond with '200 OK' at the root route"
  end

end
```

A rackup configuration file

```ruby
# config.ru
$LOAD_PATH.unshift("lib") unless $LOAD_PATH.include?("lib")
require 'foo'

run Foo
```

A `Gemfile`

```ruby
# Gemfile
source "https://rubygems.org"

gem "sinatra"

group :development do
  gem "rake"
end

group :test do
  gem "autotest"
  gem "autotest-suffix"
  gem "rack-test"
end
```

A `Rakefile`

```ruby
# Rakefile
require 'rake/testtask'

Rake::TestTask.new do |test|
  test.libs << "test" << "lib"
  test.pattern = "test/**/*_test.rb"
end
```
