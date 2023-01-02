# Code Execution

The code below is vulnureable to Code Execution

```ruby
module Jobs

  class ConfirmSnsSubscription < ::Jobs::Base
    sidekiq_options retry: false

    def execute(args)
      return unless raw = args[:raw].presence
      return unless json = args[:json].presence
      return unless subscribe_url = json["SubscribeURL"].presence

      require "aws-sdk-sns"
      return unless Aws::SNS::MessageVerifier.new.authentic?(raw)

      # confirm subscription by visiting the URL
      open(subscribe_url)
    end

  end

end
```

# Why it's vulnerable?
This code is vulnerable to a remote code execution (RCE) attack. The open() method is used to visit a URL specified in the subscribe_url variable, which is provided in the json parameter. If the json parameter is not properly validated or sanitized, it may be possible for an attacker to supply a malicious URL that could be used to execute arbitrary code on the server.

# Impact?
To exploit this vulnerability, an attacker could craft a special URL that contains malicious code, and then provide this URL as the subscribe_url in the json parameter. When the execute() method is called, the open() method will be used to visit the attacker-controlled URL, which could execute the malicious code on the server.

# How to fix?
To prevent this type of attack, it is important to carefully validate and sanitize all input parameters, and to avoid using potentially dangerous methods such as open() unless they are absolutely necessary. It may also be necessary to implement additional security measures, such as input filtering and whitelisting, to prevent attackers from supplying malicious URLs.
