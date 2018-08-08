# Pivotal Cloud Cache @Cacheable REST Demo

Pivotal Cloud Cache (PCC) is a high-performance caching layer for Pivotal Cloud Foundry (PCF). PCC offers an in-memory key-value store. It delivers low-latency responses to a large number of concurrent data access requests and delivers on near linear scalability.

PCC provides a service broker to create in-memory data clusters on demand. These clusters are dedicated to the PCF space and tuned for specific use cases defined by the plan. Service operators can create multiple plans to support different use cases.

PCC uses Pivotal GemFire. 

# How to use the project

1 - Create the service instance
```
$ cf create-service p-cloudcache dev-plan mypcc
```
2 - Create the service key 
```
$ cf create-service-key mypcc mypcc_service_key
```
3 - Using the GemFire command line tool ``gfsh`` create the region with the policy that we want for the data.

```
$ gfsh
    _________________________     __
   / _____/ ______/ ______/ /____/ /
  / /  __/ /___  /_____  / _____  / 
 / /__/ / ____/  _____/ / /    / /  
/______/_/      /______/_/    /_/    

Monitor and Manage Pivotal GemFire

gfsh>connect --use-http=true --url=https://yoururl/gemfire/v1 --user=you_username --password=your_password
gfsh>create region --name=test --type=PARTITION  --entry-time-to-live-expiration=60 --entry-time-to-live-expiration-action=destroy --enable-statistics=true
```

4 - cf push

# Related Projects

* https://pivotal.io/pivotal-gemfire
* http://geode.apache.org/
* https://projects.spring.io/spring-data-gemfire/
* **@Cacheable** is part of the Spring Context Library - https://spring.io/projects/spring-framework

# Youtube

For a video walk through and demo check out this youtube video: https://youtu.be/FV2ZmSvoY1E