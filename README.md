cfndsl-templates
======

In Tab most services are deployed using [AWS Cloudformation](http://docs.amazonwebservices.com/AWSCloudFormation/latest/UserGuide/GettingStarted.html) templates. These templates are incredibly powerful way to create resources in AWS. 
However using AWS CLI or Native Cloudformation to deploy services is cumbersome and not scalable.
Thus to give us more programitcal freedom and scalability we are using cfndsl gem which is a simple DSL. 

The cfndsl-templates library allows us to write eqivalent templates in a more friendly (yaml) laungage and generate the correct json cloudformation by running ruby gem commands.


## Getting Started

ruby version > 2.3.0 is required to run cfndsl, you should look at using rbenv example for installing with rbenv

    rbenv exec gem install cfndsl

Example for doing it system wide Ruby

    sudo gem install cfndsl

Update the the cloudformation specification to the latest version.

    cfndsl -u

or update to a specific version

    cfndsl -u 2.21.0

Now write a template in the dsl

```ruby

CloudFormation {
  Description "Test"

  Parameter("One") {
    String
    Default "Test"
	MaxLength 15
  }

  Output(:One,FnBase64( Ref("One")))

  EC2_Instance(:MyInstance) {
    ImageId "ami-12345678"
  }

}
```
