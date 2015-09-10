## Problem

Installing POSTFIX with AWS SES on Ubuntu 14.04

## Solution

Most of the AWS documentation is correct. The only main discrepency is with the SES server name. The documentation surrounds the server with `[` and `]` and its not required for it to work. The purpose of the brackets is to signify the enclosed values could change. This isn't noted anywhere in the documentation.

On Ubuntu 14.04 I also need to install a couple of different packages for the authentication to work correctly.

```$ apt-get update && apt-get install -y postfix mailutils libsasl2-dev libsasl2-modules sasl2-bin gsasl```

Following the AWS docs on [Postfix](http://docs.aws.amazon.com/ses/latest/DeveloperGuide/postfix.html) from this point on should be successful.
