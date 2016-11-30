# Configuration File Conventions

This document defines the convention we should follow in our configuration files to make sure they are using a consistent format across all the services.

## Example

```json
{
  "environment": "production",
  "logger": {
    "name": "myservice",
    "streams":[{
      "type": "stream",
      "level":"info",
      "stream": "process.out"
    }]
  },
  "services": {
    "mongodb": {
      "host": "localhost",
      "port": 27017,
      "db_name": "myservice",
      "replicaset_name": "",
      "authentication": {
        "db": "admin",
        "username": "admin",
        "password": "admin"
      }
    },
    "redis": {
      "host": "localhost",
      "authentication": {
        "username": "admin",
        "password": "admin"
      }
    },
    "another_service": {
      "url": "http://another_service:9001",
      "authentication": {
        "api_key": "myserviceapikey"
      }
    }
  },
  "configurations": {
    "port": 9000
  }
}
```

## Format

The configuration file should be in JSON format.

## Top level properties

### environment

The running environment of the service, should be either production, test, or development.

### logger

Most services will use logging frameworks to log messages, and most of those logging frameworks requires some sort of configurations. For nodejs services, we use Bunyan logger by default. The example above demostrates the expected format to configure the Bunyan logger.

The service should write the log messages to the stdout of the process and the launch script of the service will pipe the output to log files. The rotation of the log files are managed by the system logrotate service.

### services

This section is used to describe all the dependent services. E.g. databases, caches etc. The key should be the name of the service, and the value should specify the connection details of the service, including any required credentials.

### configurations

Any other configuration options should be specified here, e.g. port number etc.

## Other naming conventions

* all the keys should be lowercase
* for key with multiple words, use "_" instead of "-" or camelcase.
* for credentials
  * prefer "username" over "user"
  * prefer "password" over "pass" or "passwd"
* for connection details of dependent services
  * use "url" to specify the full link, including protocol, host and port. Use "," to separate multiple urls.
  * otherwise use "protocol", "host", "port" to specify each individual part. Use "," to separate multiple hosts. 




