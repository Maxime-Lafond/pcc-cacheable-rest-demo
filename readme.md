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

# What about GemFire

I have added an `application.yml` file for a `dev` profile which assumes `dev`  will be using a local GemFire instance.  Spring will pick up that YML file and use to configure GemFire client to properly connect a GemFire system.   

By using this method there are no code changes needed when switching between GemFire and PCC.   

To help test I have included a [GemFire start script](scripts/start_gemfire.sh) that starts up GemFire with an example authentication and authorization implementation.   

To run the `dev` profile which will connect to local GemFire system just tell spring that we are using the `dev` profile:
```
java -Dspring.profiles.active=dev -jar demo-0.0.1-SNAPSHOT.jar 
```

Relevant files:
* [User/Password/Role Security File](config/security.json) 
* [Appliacation YML file for development purposes](src/main/resources/application.yml)

## Spring Profiles

When deploying to PCF the spring boot application is started with a `cloud` profile.   I have purposely left the configuration blank for `cloud` since it gets injected via PCF when the application starts.   

If you have a GemFire cluster in production and you would like to connect to it VIA PCF just configure the GemFire properties for the `cloud` profile.   Or we can insert those connection details as a [user provided service](https://docs.cloudfoundry.org/devguide/services/user-provided.html).   

If you make the [user provided service](https://docs.cloudfoundry.org/devguide/services/user-provided.html) look like PCC then spring will do all of the hard work for you.

Example PCC user provided service document:

```json
{
  "p-cloudcache": [
    {
      "credentials": {
    "locators": [
      "10.244.0.4[55221]",
      "10.244.1.2[55221]",
      "10.244.0.130[55221]"
    ],
    "urls": {
      "gfsh": "http://cloudcache-5574c540-1464-4f9e-b56b-74d8387cb50d.local.pcfdev.io/gemfire/v1",
      "pulse": "http://cloudcache-5574c540-1464-4f9e-b56b-74d8387cb50d.local.pcfdev.io/pulse"
    },
    "users": [
      {
        "password": "some_developer_password",
        "username": "developer"
      },
      {
        "password": "some_password",
        "username": "cluster_operator"
      }
    ]
      },
      "label": "p-cloudcache",
      "name": "test-service",
      "plan": "caching-small",
      "provider": null,
      "syslog_drain_url": null,
      "tags": [],
      "volume_mounts": []
    }
  ]
}
```