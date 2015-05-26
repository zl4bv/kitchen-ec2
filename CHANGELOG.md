## 0.9.2 / 2015-05-26

### Bug Fixes

* Pull Request [#131][]: Adding back support for a proxy via config `http_proxy` which defaults to `ENV['HTTP_PROXY']`
* Pull Request [#128][]: 
    * Fixes [#121][]: Fixing error `Network interfaces and an instance-level security groups may not be specified on the same request` 
    * Fixes [#127][]: User Data content should be base64 encoded when passed to aws sdk 

### New Features

### Improvements

## 0.9.1 / 2015-05-21

### Bug Fixes

* Pull Request [#124][]: AWS SDK V2 returns `instance.public_dns_name` as empty string instead of nil, and we were only checking for nil.  Caused timeouts trying to connect. ([@tyler-ball][])
    * Fixed regression: Adding back `interface` config value that I accidently removed, code is now in line with README.
* Pull Request [#125][]: When specifying `associate_public_ip` we must send the subnet (if provided) in the `network_interfaces` section of the payload instead of the main section. ([@tyler-ball][])
    * Fixed regression: Accidently renamed config `associate_public_ip` to `associate_public_ip_address`, reverting.
    * Fixed regression: Accidently renamed config `iam_profile_name` to `iam_instance_profile`, reverting.

### New Features

### Improvements

## 0.9.0 / 2015-05-18

### Bug Fixes

* Pull Request [#46][]: Don't create multiple instances if `kitchen create` is called multiple times. ([@anl][])
* Pull Request [#97][], [#69][], [#99][]: Try additional connections to servers which don't have a `public_ip_address`.  This helps connect to nodes over VPN. ([@chuckg][], [@tyler-ball][], [@mumoshu][])

### New Features

* Pull Request [#35][]: Adding support for specifying the IAM profile on created instance.  Set `:iam_profile_name` in the driver section of your .kitchen.yml to specify this. ([@nicgrayson][])
* Pull Request [#82][]: Add the ability to specify user data.  Set `:user_data` in the driver section of your .kitchen.yml.  This can either be the user data or the path to a file which contains the user data. ([@sebbrandt87][])
* Pull Request [#84][]: Add the ability to specify the private ip of the instance.  Set `:private_ip_address` in the driver section of your .kitchen.yml. ([@scarolan][])
* Pull Request [#68][], [#104][], [#107][]: If provisioning from an EC2 host and credentials are not set use the local nodes's credentials.  If access key & secret are set, do not use local session token - leave it unset. ([@JamesAwesome][], [@Igorshp][], [@daanemanz][])

### Improvements

* Pull Request [#110][]: Updating to use the AWS SDK V2 instead of Fog. ([@tyler-ball][])
    * We no longer recommend storing the AWS credentials in the `.kitchen.yml` file.  Instead, specify them as environment variables or in the `~/.aws/credentials` file.  See the README for more details.
* Pull Request [#112][]: Updating to depend on the latest version of Test Kitchen, 1.4.0. ([@jmundrawala][])

## 0.8.0 / 2014-02-11

### Bug fixes

* Pull request [#29][], pull request [#28][]: Relax Test Kitchen dependency to allow versions 1.0 and up. ([@coderanger][], [@someara][])

## New features

* Pull request [#29][]: Add `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` fallback environment variable support. ([@coderanger][])
* Pull request [#27][], pull request [#31][]: Make EC2 endpoint configurable for services implementing the AWS APIs. ([@bozinsky][], [@spheromak][])
* Pull request [#34][]: Support AWS session tokens for use with IAM roles. ([@coderanger][])
* Pull request [#22][]: Add ebs_optimized attribute. ([@tiwilliam][])

### Improvements

* Pull request [#20][], pull request [#21][], issue [#14][]: Add configurability around whether to use DNS name, public or private IP addresses when connecting to instances. ([@matheeeny][], [@Atalanta][], [@fnichol][])
* Pull request [#15][]: In VPC groups must be specified by setting :security_group_ids rather than :groups. ([@eherot][])
* Pull request [#23][]: README updates for badges. ([@sethvargo][], [@arangamani][])


## 0.7.0 / 2013-08-29

### Bug fixes

* Pull request [#13][]: #wait_for_ssh takes 2 arguments. ([@dysinger][])

### Improvements

* Support computed defaults for a select list of pre-determined platforms (see readme for quick example). ([@fnichol][])~


## 0.6.0 / 2013-07-23

### Bug fixes

* Pull request [#8][]: Use private ip if the public ip is `nil`. ([@dissonanz][])

### Improvements

* Pull request [#9][]: Match access and secret key env vars in example kitchen config with CLI tools' env vars. ([@juliandunn][])


## 0.5.1 / 2013-05-23

### New features

* Pull request [#7][]: Add subnet\_id option for use with VPC. ([@dissonanz][])


## 0.5.0 / 2013-05-23

### New features

* Pull request [#5][]: Add the ability to give ec2 instances tags. ([@halcyonCorsair][])
* Add required_config attributes for driver. ([@fnichol][])

### Improvements

* Write README. ([@fnichol][])
* Pull request [#2][]: Extra EC2 server creation debugging. ([@mattray][])
* Remove default_config :port in favor of SSHBase default (also 22). ([@fnichol][])

<!--- The following link definition list is generated by PimpMyChangelog --->
[#2]: https://github.com/test-kitchen/kitchen-ec2/issues/2
[#5]: https://github.com/test-kitchen/kitchen-ec2/issues/5
[#7]: https://github.com/test-kitchen/kitchen-ec2/issues/7
[#8]: https://github.com/test-kitchen/kitchen-ec2/issues/8
[#9]: https://github.com/test-kitchen/kitchen-ec2/issues/9
[#13]: https://github.com/test-kitchen/kitchen-ec2/issues/13
[#14]: https://github.com/test-kitchen/kitchen-ec2/issues/14
[#15]: https://github.com/test-kitchen/kitchen-ec2/issues/15
[#20]: https://github.com/test-kitchen/kitchen-ec2/issues/20
[#21]: https://github.com/test-kitchen/kitchen-ec2/issues/21
[#22]: https://github.com/test-kitchen/kitchen-ec2/issues/22
[#23]: https://github.com/test-kitchen/kitchen-ec2/issues/23
[#27]: https://github.com/test-kitchen/kitchen-ec2/issues/27
[#28]: https://github.com/test-kitchen/kitchen-ec2/issues/28
[#29]: https://github.com/test-kitchen/kitchen-ec2/issues/29
[#31]: https://github.com/test-kitchen/kitchen-ec2/issues/31
[#34]: https://github.com/test-kitchen/kitchen-ec2/issues/34
[#35]: https://github.com/test-kitchen/kitchen-ec2/issues/35
[#46]: https://github.com/test-kitchen/kitchen-ec2/issues/46
[#68]: https://github.com/test-kitchen/kitchen-ec2/issues/68
[#69]: https://github.com/test-kitchen/kitchen-ec2/issues/69
[#82]: https://github.com/test-kitchen/kitchen-ec2/issues/82
[#84]: https://github.com/test-kitchen/kitchen-ec2/issues/84
[#97]: https://github.com/test-kitchen/kitchen-ec2/issues/97
[#99]: https://github.com/test-kitchen/kitchen-ec2/issues/99
[#104]: https://github.com/test-kitchen/kitchen-ec2/issues/104
[#107]: https://github.com/test-kitchen/kitchen-ec2/issues/107
[#110]: https://github.com/test-kitchen/kitchen-ec2/issues/110
[#112]: https://github.com/test-kitchen/kitchen-ec2/issues/112
[#121]: https://github.com/test-kitchen/kitchen-ec2/issues/121
[#124]: https://github.com/test-kitchen/kitchen-ec2/issues/124
[#125]: https://github.com/test-kitchen/kitchen-ec2/issues/125
[#127]: https://github.com/test-kitchen/kitchen-ec2/issues/127
[#128]: https://github.com/test-kitchen/kitchen-ec2/issues/128
[#131]: https://github.com/test-kitchen/kitchen-ec2/issues/131
[@Atalanta]: https://github.com/Atalanta
[@Igorshp]: https://github.com/Igorshp
[@JamesAwesome]: https://github.com/JamesAwesome
[@anl]: https://github.com/anl
[@arangamani]: https://github.com/arangamani
[@bozinsky]: https://github.com/bozinsky
[@chuckg]: https://github.com/chuckg
[@coderanger]: https://github.com/coderanger
[@daanemanz]: https://github.com/daanemanz
[@dissonanz]: https://github.com/dissonanz
[@dysinger]: https://github.com/dysinger
[@eherot]: https://github.com/eherot
[@fnichol]: https://github.com/fnichol
[@halcyonCorsair]: https://github.com/halcyonCorsair
[@jmundrawala]: https://github.com/jmundrawala
[@juliandunn]: https://github.com/juliandunn
[@matheeeny]: https://github.com/matheeeny
[@mattray]: https://github.com/mattray
[@mumoshu]: https://github.com/mumoshu
[@nicgrayson]: https://github.com/nicgrayson
[@scarolan]: https://github.com/scarolan
[@sebbrandt87]: https://github.com/sebbrandt87
[@sethvargo]: https://github.com/sethvargo
[@someara]: https://github.com/someara
[@spheromak]: https://github.com/spheromak
[@tiwilliam]: https://github.com/tiwilliam
[@tyler-ball]: https://github.com/tyler-ball