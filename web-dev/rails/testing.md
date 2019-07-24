# Testing

## Use rspec with Rails

TODO: Write about why rspec over minitest

## Time Travel

There's a great gem called [timecop](https://github.com/travisjeffery/timecop) but since a few versions ago Rails has its own implementation that is slightly more lightweight but also covers most common use cases.

The module in question is `ActiveSupport::Testing::TimeHelpers` and to use it you should first include it in your `rails_helper.rb` file:

```ruby
RSpec.configure do |config|
  # ... your other settings
  config.include ActiveSupport::Testing::TimeHelpers
end
```

You can then easily use `freeze_time`, `travel` and `travel_to` methods, which comes in very handy when dealing with integration and unit tests that are time related. For bonus points you can wrap it up in a block to never worry about having to restore previous state again.

Here are a few examples:

```ruby
freeze_time do
  time1 = Time.now
  sleep 2
  time2 = Time.now
  time1 == time2 # => true
end

Time.now # => 2019-07-24 22:56:29 +0200
travel 1.day do
  p Time.now # => 2019-07-25 22:56:29 +0200
end

travel_to Time.zone.local(2019, 2, 2, 9, 9, 0) do
  p Time.now # => Left as an exercise to the reader
end
```

