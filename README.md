# VeryFake

Sometimes fakes are a good choice. But the price is high. In particular, they make changing code harder. You rename a method, but all tests that stub the previous version _keep_ passing. It would be nice if those started to fail so that when they're green again, you can be certain that everything that had to be updated was updated. Well, now you can.

To be fare, you already can in Rspec with their [veryfying double](https://relishapp.com/rspec/rspec-mocks/v/3-9/docs/verifying-doubles). But what about Minitest? And there are still subtle differences. Unlike Rspec verifying double:

- here you need to supply real objects to create fakes. That's contraversial - as it goes against the idea of testing in isolation - but realistically, at least in Rails, everything is loaded anyway, so that's a moot point;
- very_fake is aware of autogenerated methods (e.g. ActiveRecord model accessors);
- very_fake has the same API to stub class and instance methods;
- very_fake performs a full parameter check, comparing required, optional and keyword arguments (veryfing double only checks arity).

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'very_fake'
```

And then execute:

    $ bundle install

## Usage

```ruby
require 'very_fake'

# Create fake instance of `Time`
now = VeryFake.new(Time.now)

# Throws "Can't stub non-existent method Time#bar (VeryFake::Error)"
now.stub(:bar)

# Still throws: "Expected Time#zone stub to accept (), but was (req) (VeryFake::Error)"
now.stub(:zone) { |arg| }

# All good now!
now.stub(:zone) { 'XYZ' }
now.zone # => 'XYZ'

# Makeshift mock
invoked = false

now.stub(:strftime) do |fmt|
  # A bit of spying for good measure
  assert(fmt.is_a?(String))

  invoked = true
end

assert_changes('invoked') { now.strftime('zyx') }

# Stubbing class methods is no different
time = VeryFake.new(Time)

time.stub(:now) { 'XYZ' }
time.now # => 'XYZ'
```

Note: Minitest is required for `assert*` to work.

## Development

After checking out the repo, run `bundle install` to install dependencies. Then, run `bundle exec rspec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/featurist/very_fake.

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).
