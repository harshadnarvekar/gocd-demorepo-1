cfndsl-templates
======

In Tab most services are deployed using [AWS Cloudformation](http://docs.amazonwebservices.com/AWSCloudFormation/latest/UserGuide/GettingStarted.html) templates. These templates are incredibly powerful way to create resources in AWS. 
However using AWS CLI or Native Cloudformation to deploy services is cumbersome and not scalable.
Thus to give us more programitcal freedom and scalability we are using cfndsl gem which is a simple DSL. 

The cfndsl-templates library allows us to write eqivalent templates in a more friendly (yaml) laungage and generate the correct json cloudformation by running ruby gem commands.


## Getting Started

We need to have cloned 3 repos:
1. [cfndsl-templates](https://github.tabcorp.com.au/TabDigital/cfndsl-templates/tree/beta)
2. [deployment-libraries](https://github.tabcorp.com.au/TabDigital/deployment-libraries/tree/v3)

Note: Each channel will have its own deployment repo. For example below is the deployment repo for Customer Channel
3. [deployment-customer-channel](https://github.tabcorp.com.au/tabdigital/deployment-customer-channel)

Now lets write a simple template in yaml format

```yaml

template: 'ecs'
description: 'Deploys trawl containerised service to aws ecs'
appName: 'service-wift-trawl'
appConfigPrefix: 'TAB'
tags:
  costCentre: '5607'
  schedule: '24X7'
  owner: 'TabTechDigitalPlatformTribe@tabcorp.com.au'
ecsTask:
  cpu: 1024
  memory: 1024
  port: 8080
  healthCheck:
    internval: '5s'
    timeout: '3s'
    route: '/v1/wift-service/status/details'
  ulimit:
    hard: 16384
    soft: 16384
  autoScaling:
    targetValue: 50
  role: service-wift-trawl-task-role
  command: [ "/bin/sh", "-c", "cd /app && npm start" ]
```

However, they can accept all of the following additional keys per the [documentation](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html):

```ruby
Parameter('foo') do
  Description           'This is a sample parameter definition'
  Type                  'String'
  Default               'foo'
  NoEcho                true
  AllowedValues         %w(foo bar)
  AllowedPattern        '/pattern/'
  MaxLength             5
  MinLength             3
  MaxValue              10
  MinValue              2
  ConstraintDescription 'The error message printed when a parameter outside the constraints is given'
end
```
