# HPFeeds

This gem should be used to make easy to interact (publish, subscribe) with a [HPFeeds broker](https://redmine.honeynet.org/projects/hpfeeds/wiki).

## Installation

Add this line to your application's Gemfile:

    gem 'hpfeeds'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install hpfeeds

## Usage

Here is a basic example:

```ruby
require "hpfeeds"

def on_data(name, chan, payload)
  puts "[%s] %s: %s" % [ chan, name, payload ]
  # just an example here...
  @hp.publish('channel', 'message')
end

def on_error(data)
  STDERR.puts "ERROR: " + data.inspect
end

begin
  @hp = HPFeeds::Client.new ({
    host: hpfeeds_server_name_here,
    port:   10000,
    ident:  'XXXX',
    secret: '123456'
  })
  channels = %w[ chan1 chan2 chanN ]
  @hp.subscribe(*channels)
  @hp.run(method(:on_data), method(:on_error))

rescue => e
  puts "Exception: #{e}"
ensure
  @hp.close if @hp
end
```
It's also possibile to set separate handlers for messages from different channels, as follows:
```ruby
@hp.subscribe(chan1, chan2) do
  puts "Received something"
end

@hp.subscribe(chan3, chan4, chan5) do |name, chan|
  puts "Received something on #{chan}, from #{name}"
end

@hp.subscribe(chan6, chan7) { |name, chan, payload| custom_method(name, chan, payload) }
```
In this case the first argument in ```@hp.run(method(:on_data), method(:on_error))``` is just a deafult handler, used for messages coming from unhandled channels.
## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
