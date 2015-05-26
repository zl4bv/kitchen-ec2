# <a name="title"></a> Kitchen::Ec2: A Test Kitchen Driver for Amazon EC2

[![Gem Version](https://badge.fury.io/rb/kitchen-ec2.png)](http://badge.fury.io/rb/kitchen-ec2)
[![Build Status](https://travis-ci.org/test-kitchen/kitchen-ec2.png)](https://travis-ci.org/test-kitchen/kitchen-ec2)
[![Code Climate](https://codeclimate.com/github/test-kitchen/kitchen-ec2.png)](https://codeclimate.com/github/test-kitchen/kitchen-ec2)

A [Test Kitchen][kitchenci] Driver for Amazon EC2.

This driver uses the [aws sdk gem][aws_sdk_gem] to provision and destroy EC2
instances. Use Amazon's cloud for your infrastructure testing!

## Requirements

There are **no** external system requirements for this driver. However you
will need access to an [AWS][aws_site] account.

## Installation and Setup

Please read the [Driver usage][driver_usage] page for more details.

## Default Configuration

This driver can determine AMI and username login for a select number of
platforms in each region. Currently, the following platform names are
supported:

```ruby
---
platforms:
  - name: ubuntu-10.04
  - name: ubuntu-12.04
  - name: ubuntu-12.10
  - name: ubuntu-13.04
  - name: ubuntu-13.10
  - name: ubuntu-14.04
  - name: centos-6.4
  - name: debian-7.1.0
```

This will effectively generate a configuration similar to:

```ruby
---
platforms:
  - name: ubuntu-10.04
    driver:
      image_id: ami-1ab3ce73
      username: ubuntu
  - name: ubuntu-12.04
    driver:
      image_id: ami-2f115c46
      username: ubuntu
  # ...
  - name: centos-6.4
    driver:
      image_id: ami-bf5021d6
      username: root
  # ...
```

For specific default values, please consult [amis.json][amis_json].

## Authenticating with AWS

There are 3 ways you can authenticate against AWS, and we will try them in the
following order:

1. You can specify the access key and access secret (and optionally the session
token) through config.  See the `aws_access_key_id` and `aws_secret_access_key`
config sections below to see how to specify these in your .kitchen.yml or
through environment variables.  If you would like to specify your session token
use the environment variable `AWS_SESSION_TOKEN`.
1. The shared credentials ini file at `~/.aws/credentials`.  You can specify 
multiple profiles in this file and select one with the `AWS_PROFILE`
environment variable or the `shared_credentials_profile` driver config.  Read
[this][credentials_docs] for more information.
1. From an instance profile when running on EC2.  This accesses the local
metadata service to discover the local instance's IAM instance profile.

This precedence order is taken from http://docs.aws.amazon.com/sdkforruby/api/index.html#Configuration
  
The first method attempted that works will be used.  IE, if you want to auth
using the instance profile, you must not set any of the access key configs
or environment variables, and you must not specify a `~/.aws/credentials`
file.

Because the Test Kitchen test should be checked into source control and ran
through CI we no longer recommend storing the AWS credentials in the
`.kitchen.yml` file.  Instead, specify them as environment variables or in the 
`~/.aws/credentials` file.

## General Configuration

### availability\_zone

The AWS [availability zone][region_docs] to use.  Only request
the letter designation - will attach this to the region used.

The default is `"#{region}b"`.

### aws\_access\_key\_id

**Deprecated** It is recommended to use the `AWS_ACCESS_KEY_ID` or the
`~/.aws/credentials` file instead.

The AWS [access key id][credentials_docs] to use.

### aws\_secret\_access\_key

**Deprecated** It is recommended to use the `AWS_SECRET_ACCESS_KEY` or the
`~/.aws/credentials` file instead.

The AWS [secret access key][credentials_docs] to use.

### shared\_credentials\_profile

The EC2 [profile name][credentials_docs] to use when reading credentials out
of `~/.aws/credentials`.  If it is not specified AWS will read the `Default`
profile credentials (if using this method of authentication).

Can also be specified as `ENV['AWS_PROFILE']`.

### aws\_ssh\_key\_id

**Required** The EC2 [SSH key id][key_id_docs] to use.

The default will be read from the `AWS_SSH_KEY_ID` environment variable if set,
or `nil` otherwise.

### aws\_session\_token

**Deprecated** It is recommended to use the `AWS_SESSION_TOKEN` or the
`~/.aws/credentials` file instead.

The AWS [session token][credentials_docs] to use.

### flavor\_id

**Deprecated** See [instance_type](#config-instance_type) below.

### <a name="config-instance_type"></a> instance\_type

The EC2 [instance type][instance_docs] (also known as size) to use.

The default is `"m1.small"`.

### security_group_ids

An Array of EC2 [security groups][group_docs] which will be applied to the
instance.

The default is `["default"]`.

### image\_id

**Required** The EC2 [AMI id][ami_docs] to use.

The default will be determined by the `aws_region` chosen and the Platform
name, if a default exists (see [amis.json][ami_json]). If a default cannot be
computed, then the default is `nil`.

### region

**Required** The AWS [region][region_docs] to use.

If the environment variable `AWS_REGION` is populated that will be used.
Otherwise the default is `"us-east-1"`.

### subnet\_id

The EC2 [subnet][subnet_docs] to use.

The default is unset, or `nil`.

### tags

The Hash of EC tag name/value pairs which will be applied to the instance.

The default is `{ "created-by" => "test-kitchen" }`.

### user_data

The user_data script or the path to a script to feed the instance.
Use bash to install dependencies or download artifacts before chef runs.
This is just for some cases. If you can do the stuff with chef, then do it with
chef!

The default is unset, or `nil`.

### iam\_profile\_name

The EC2 IAM profile name to use.

The default is `nil`.

### price

The price you bid in order to submit a spot request. An additional step will be required during the spot request process submission. If no price is set, it will use an on-demand instance.

The default is `nil`.

### http\_proxy

Specify a proxy to send AWS requests through.  Should be of the format `http://<host>:<port>`.

The default is `ENV['HTTP_PROXY']`

**Note** - The AWS command line utility allow you to specify [two proxies](http://docs.aws.amazon.com/cli/latest/userguide/cli-http-proxy.html), one for HTTP and one for HTTPS.  The AWS Ruby SDK only allows you to specify 1 proxy and because all requests are `https://` this proxy needs to support HTTPS.

## Disk Configuration

### ebs\_volume\_size

**Deprecated** See [block_device_mappings](#config-block_device_mappings) below.

Size of ebs volume in GB.

### ebs\_delete\_on\_termination

**Deprecated** See [block_device_mappings](#config-block_device_mappings) below.

`true` if you want ebs volumes to get deleted automatically after instance is terminated, `false` otherwise

### ebs\_device\_name

**Deprecated** See [block_device_mappings](#config-block_device_mappings) below.

name of your ebs device, for example: `/dev/sda1`

### <a name="config-block_device_mappings"></a> block\_device\_mappings

A list of block device mappings for the machine.  An example of all available keys looks like:
```yaml
block_device_mappings:
  - ebs_device_name: /dev/sda1
    ebs_volume_size: 20
    ebs_delete_on_termination: true
  - ebs_device_name: /dev/sda2
    ebs_volume_type: gp2
    ebs_virtual_name: test
    ebs_volume_size: 15
    ebs_delete_on_termination: true
    ebs_snapshot_id: snap-0015d0bc
```

The keys `ebs_device_name`, `ebs_volume_size` and `ebs_delete_on_termination` are required for every mapping.
For backwards compatiability a default `block_device_mappings` will be created if none are listed and the deprecated
storage config keys are present.

The keys `ebs_volume_type`, `ebs_virtual_name` and `ebs_snapshot_id` are optional.  See
[Amazon EBS Volume Types](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) to find out more about
volume types. `ebs_volume_type` defaults to `standard` but can also be `gp2` or `io1`.

If you have a block device mapping with a `ebs_device_name` equal to the root storage device name on your
[image](#config-image-id) then the provided mapping will replace the settings in the image.

If this is not provided it will use the default block_device_mappings from the AMI.

### ebs\_optimized

Option to launch EC2 instance with optimized EBS volume. See
[Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/) to find
out more about instance types that can be launched as EBS-optimized instances.

The default is `false`.

## Network and Communication Configuration

### associate\_public\_ip

AWS does not automatically allocate public IP addresses for instances created
within non-default [subnets][subnet_docs]. Set this option to `true` to force
allocation of a public IP and associate it with the launched instance.

If you set this option to `false` when launching into a non-default
[subnet][subnet_docs], Test Kitchen will be unable to communicate with the
instance unless you have a VPN connection to your
[Virtual Private Cloud][vpc_docs].

The default is `true` if you have configured a [subnet_id](#config-subnet-id),
or `false` otherwise.

### private\_ip\_address

The primary private IP address of your instance.

If you don't set this it will default to whatever DHCP address EC2 hands out.

### interface

The place from which to derive the hostname for communicating with the instance.  May be `dns`, `public` or `private`.  If this is unset, the driver will derive the hostname by failing back in the following order:

1. DNS Name
2. Public IP Address
3. Private IP Address

The default is unset.

### ssh\_key

**Deprecated** Instead use the `transport.ssh_key` like

```ruby
transport:
  ssh_key: ~/.ssh/id_rsa
```

Path to the private SSH key used to connect to the instance.

The default is unset, or `nil`.

### ssh\_timeout

**Deprecated** Instead use the `transport.connection_timeout` like

```ruby
transport:
  connection_timeout: 60
```

The number of seconds to sleep before trying to SSH again.

The default is `1`.

### ssh\_retries

**Deprecated** Instead use the `transport.connection_retries` like

```ruby
transport:
  connection_retries: 10
```

The number of times to retry SSH-ing into the instance.

The default is `3`.

### username

**Deprecated** Instead use the `transport.username` like

```ruby
transport:
  username: ubuntu
```

The SSH username that will be used to communicate with the instance.

The default will be determined by the Platform name, if a default exists (see
[amis.json][amis_json]). If a default cannot be computed, then the default is
`"root"`.

## Example

The following could be used in a `.kitchen.yml` or in a `.kitchen.local.yml`
to override default configuration.

```yaml
---
driver:
  name: ec2
  aws_ssh_key_id: id_rsa-aws
  security_group_ids: ["sg-1a2b3c4d"]
  region: us-east-1
  availability_zone: b
  require_chef_omnibus: true
  subnet_id: subnet-6d6...
  iam_profile_name: chef-client
  instance_type: t2.micro
  associate_public_ip: true
  private_ip_address: 10.0.0.27
  interface: dns
  block_device_mappings:
    - ebs_device_name: /dev/sda0
      ebs_volume_type: gp2
      ebs_virtual_name: test
      ebs_volume_size: 15
      ebs_delete_on_termination: true

transport:
  ssh_key: /path/to/id_rsa-aws
  connection_timeout: 10
  connection_retries: 5
  username: ubuntu

platforms:
  - name: ubuntu-12.04
    driver:
      image_id: ami-fd20ad94
      username: ubuntu
  - name: centos-6.3
    driver:
      image_id: ami-ef5ff086
      username: ec2-user

suites:
# ...
```

## <a name="development"></a> Development

* Source hosted at [GitHub][repo]
* Report issues/questions/feature requests on [GitHub Issues][issues]

Pull requests are very welcome! Make sure your patches are well tested.
Ideally create a topic branch for every separate change you make. For
example:

1. Fork the repo
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

## <a name="authors"></a> Authors

Created and maintained by [Fletcher Nichol][author] (<fnichol@nichol.ca>)

## <a name="license"></a> License

Apache 2.0 (see [LICENSE][license])


[author]:           https://github.com/fnichol
[issues]:           https://github.com/test-kitchen/kitchen-ec2/issues
[license]:          https://github.com/test-kitchen/kitchen-ec2/blob/master/LICENSE
[repo]:             https://github.com/test-kitchen/kitchen-ec2
[driver_usage]:     http://docs.kitchen-ci.org/drivers/usage
[chef_omnibus_dl]:  http://www.getchef.com/chef/install/

[amis_json]:        https://github.com/test-kitchen/kitchen-ec2/blob/master/data/amis.json
[ami_docs]:         http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ComponentsAMIs.html
[aws_site]:         http://aws.amazon.com/
[credentials_docs]: http://blogs.aws.amazon.com/security/post/Tx3D6U6WSFGOK2H/A-New-and-Standardized-Way-to-Manage-Credentials-in-the-AWS-SDKs
[aws_sdk_gem]:      http://docs.aws.amazon.com/sdkforruby/api/index.html
[group_docs]:       http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html
[instance_docs]:    http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html
[key_id_docs]:      http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/verifying-your-key-pair.html
[kitchenci]:        http://kitchen.ci/
[region_docs]:      http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html
[subnet_docs]:      http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnet.html
[vpc_docs]:         http://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/ExerciseOverview.html
