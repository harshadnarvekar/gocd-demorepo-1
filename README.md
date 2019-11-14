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

Now lets write a simple template in yaml format:

### Template 


```yaml

template: 'ecs-alb'
description: ' Deployment template for dockerized recommendation service'
appName: 'api-service-account'
appConfigPrefix: 'TAB'
tags:
  costCentre: '5607'
  schedule: '24X7'
  owner: 'TabTechDigitalPlatformTribe@tabcorp.com.au'
ecsTask:
  cpu: 1024
  memory: 2048
  port: 8183
  healthCheck:
    internval: '5s'
    timeout: '3s'
    route: '/v1/account-service/status'
  ulimit:
    hard: 16384
    soft: 16384
  autoScaling:
    targetValue: 50
  appKeys: true
  role: UAT-Yarra-recommendation-RecommendationServiceTask-NPXKAAHE5WJY
  command: [ "/bin/sh", "-c", "cd /app && ./bin/start" ]
alb:
  name: 'test' # name of alb. not mandatory
  default: true # use shared alb ? default value is false
  dns: true # dns entry required or not?
  public: false # if only creating a new alb
  hostedZone: private # mandatory field if there is dns to be inserted in r53
  https: false 
  securityGroupRules:
    ports:
      inbound:
        tcp:
          80: ['172.30.128.0/22', '172.30.136.0/22']
          443: ['172.30.128.0/22', '172.30.136.0/22']
      outbound:
        tcp:
          32000-61000: ['172.30.136.0/22']
```

### Template Parameter
```yaml
defaultCapacity: &defaultCapacity
  MinCapacity: 1
  MaxCapacity: 2

default: &default
  ContainerImage: "{{env DOCKER_IMAGE}}"
  ECSClusterStack: "{{env ECS_CLUSTER_STACK}}"
  ECSHealthCheckLambda: eng-boostraping-ecs-service-healthc-LambdaFunction-1Q19NCU548QD5


UAT-Zambeze:
  <<: *default
  <<: *defaultCapacity
  Environment: UAT-Zambeze
  VpcId: 'vpc-0c7e32f64f5df48c5'
  RulePriority: 1065
  HostDnsRecord: 'zambeze-api-service-account.tabinternal.com.au'
  TaskRole: arn:aws:iam::093914002691:role/service-account-task-role
  ConfigAppEnv: 'TAB-UAT-Zambeze'
  AppSecretBucket: "093914002691-zambeze-secret"
```


Below is description and purpose of above fields mentioned in template.
Note: Please note that currently we are starting with aws resources and attributes which are commonly used in Tab. However we are not limited to these, and we will develop new resources and attributes based on demand.
If you need any new template / resource / attributes added, please free to contact TabTechDigitalPlatformTribe@mytabcorp.onmicrosoft.com team

# Template

```ruby
          
  template            'This is the template pattern which the service will use to deploy itself. Eg. /ecs/, /ecs-alb/, /ec2/,'
  description         'Description to be added in the clouformation template'
  appName             'App name to be used in deployment'
  appConfigPrefix     'Currently we are using prefix in Tab for config for different environment'
  tags                'It is a sub-section which holds extra tags to be inserted in each resource'
  ecsTask             'This is a sub-section which houses different attributes of ecs-Task Defintion in AWS ECS deployment'
  alb                 'This section is only required if service needs a load balancer
  
end
```
