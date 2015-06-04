### Custom Nagios Scripting:
I am going to show an example of custom nagios script for monitoring docker service and running docker containers.
Basically all you need is to create custom script which will exit on some state with it’s status code and short message that will be displayed to Nagios.

Here is list of Nagios status codes:
– 0 (OK)
– 1 (WARNING)
– 2 (CRITICAL)
– 3 (UNKNOWN)

#### Docker service monitoring script:

Note: Put these scripts in nagios/libexec directory at nagios server and at client also:

```
#!/bin/bash
# Check service docker is running or not:

var=`/usr/bin/cat /var/run/docker.pid`
if [[ "$var" -gt 0 ]]; then
        echo "OK - Docker is running"
        exit 0
else
        echo "Critical - Docker is not running"
        exit 2
fi
```

#### Running docker containers Monitoring script:
```
#!/bin/bash
# Check service docker is running or not:

var=`/usr/bin/docker ps -q |/usr/bin/wc -l`
if [[ "$var" -gt 0 ]]; then
        echo "OK - Running Dokcer containers are $var"
        exit 0
else
        echo "OK - Running containers are 0"
        exit 0
fi
```
#### Define command for these script in commands.cfg file:

```
define command {
        command_name    check_docker_container.sh
        command_line   $USER1$/check_nrpe -H $HOSTADDRESS$ -c check_docker_container.sh
}

define command {
        command_name    check_docker_images.sh
        command_line   $USER1$/check_nrpe -H $HOSTADDRESS$ -c check_docker_images.sh
}
```

#### Define commands for these script in nrpe.cfg at nagios server and client also:

```
command[check_docker_container.sh]=/usr/local/nagios/libexec/check_docker_container.sh
command[check_docker_images.sh]=/usr/local/nagios/libexec/check_docker_images.sh
```

#### Define services in host configuration file:
```
define service{
        use                             local-service
        host_name                       HOST1
        service_description             Docker
        check_command                   check_nrpe!check_docker.sh
        }

define service{
        use                             local-service
        host_name                       HOST1
        service_description             Docker-Containers
        check_command                   check_nrpe!check_docker_container.sh
        }
```
Start nrpe serviec at client and nagios service in nagios server.
