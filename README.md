# SocialMedia

## Usage
The idea is to treat social media sites like databases.  With that in mind, we'll have adapters for each each social media service
that we connect, passing the appropriate connection strings to.  Once connected, we can then query and publish to the connection using
the same method calls and parameters across the various social media services.  Of course some services will have more functionality
than others.  The library can be configured to silently ignore limited functionality or to explicitly raise errors.

The main objective with this library is to enable centralized maintenance of Profile/Account information across many social media
services and directory listing sites -- a critical component of local businesses optimizing for Google Maps local results.

Save your credentials in a YAML file (config/services.yml, for example):
```yaml
twitter:
  consumer_key:         'YOUR_CONSUMER_KEY'
  consumer_secret:      'YOUR_CONSUMER_SECRET'
  access_token:         'YOUR_ACCESS_TOKEN'
  access_token_secret:  'YOUR_ACCESS_TOKEN_SECRET'

facebook:
  app_key:    'YOUR_APP_KEY'
  app_secret: 'YOUR_APP_SECRET'

# ...
```

Load and go!

```ruby
require 'social_media'

def symbolized_keys hash
  hash.keys.each do |key|
    hash[(key.to_sym rescue key) || key] = hash.delete(key)
  end
  hash.each_pair{|k,v| hash[k] = symbolized_keys(v) if v.is_a?(Hash)}
  return hash
end

def service_configurations
  config_path = File.expand_path(File.join(File.dirname(__FILE__), '..', 'config'))
  @service_configurations ||= symbolized_keys YAML::load_file(File.join(config_path, 'services.yml'))
end

my_new_avatar_filename = "./images/avatar.png"

service_configurations.each_key do |service_name|
  options = service_configurations[service_name]
  options.merge!(not_provided_error: :silent)
  client = SocialMedia::Service::service(service_name).new options
  client.send_message "Just Rambling about Social Media"
  client.upload_profile_avatar my_new_avatar_filename
end
```

The above would iterate every service, send a text message and upload a new profile avatar (for the services that support it).
In the above example, the "not_provided_error: :silent" allows services that do not implement a specific API to silently fail.
This makes it easy to build an app that updates as many possible profile/account fields without worrying about dealing with
shortcomings of the library itself.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'social_media'
```

And then execute:
```bash
$ bundle
```

Or install it yourself as:
```bash
$ gem install social_media
```

## Contributing

So far, the following have been implemented:

  * Twitter
  * Facebook (WiP)
  * Linkedin (WiP)
  * Google+  (WiP)

The following are planned:

### Social Media Services:
  * Yahoo
  * Facebook
  * Apple
  * Yelp
  * WhitePages
  * Local.com
  * Foursquare

### Directories:
  * Google
  * Bing
  * MapQuest
  * Superpages
  * CitySearch
  * MerchantCircle
  * eLocal
  * Topic
  * YellowPageCity
  * LocalStack
  * ShowMeLocal
  * EZLocal
  * WhereTo?
  * YellowMoxie
  * CitySquares
  * LocalDatabase
  * Yellowise

### Statistics

  * Facebook: 1,712,000,000 users
  * WhatsApp 1,000,000,000 users
  * Facebook Messenger: 1,000,000,000 users
  * QQ: 899,000,000 users
  * WeChat: 806,000,000 users
  * QZone: 652,000,000 users
  * Tumblr: 555,000,000 users
  * Instagram: 500,000,000 users
  * Twitter: 313,000,000 users
  * Baidu Tieba: 300,000,000 users
  * Skype: 300,000,000 users
  * Sina Weibo: 282,000,000 users
  * Viber: 249,000,000 users
  * Line: 218,000,000 users
  * Snapchat: 200,000,000 users

## Contributing

This is an open source project.  If you don't see a service implemented that you need, then fork the project, implement, and
submit your pull request.  Make sure your PR is accompanied by specs, but *don't commit account credentials* to the repo!
That also means you should never check in your VCR cassettes.  If someone else wants to test a service, they should set up
their own accounts and generate their own VCR cassettes.

Testing is done by setting up a spec/config/services.yml which has the keyname that matches the new service's name.

You'll need to add a new:
  * lib/service/new_service.rb
  * spec/lib/service/new_service_spec.rb
  * connection credentials to spec/config/services.yml
  * any gem dependencies for the new service if you use an API/SDK library.

## License
The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).
