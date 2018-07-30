# Jenkins Compose

Sets up a dockerized Jenkins environment with a SonarQube-Server and a Selenium-Grid with a Firefox and Chrome Slave.
The stack is setup behind a nginx reverse proxy that handles SSL communication.


## Prerequisits
 - Docker 1.13+
 - Docker-Compose 1.10+
 - A valid SSL certificate and key

## Installation

Clone the repositiory
```sh
git clone https://github.com/WowMuchName/jenkins-compose.git
```

Create a cert-volume
```sh
docker volume create certs
```

Put the your certificate an key into the volume
```sh
cp certificate.crt /var/lib/docker/certs/_data/
cp certificate.key /var/lib/docker/certs/_data/
```

Alternativelly any mechanism that populates the _cert_ volume is fine.

Note that nginx expects your certificates to be bundled. Also ensure the proper ownership and permission on the certificate files.


Start the stack
```sh
docker-compose up
```

## Usage

- Jenkins is at https://yourhost:444
- Sonarqube is at https://yourhost:445
- Jenkins Slaves can connect via https://yourhost:5000


## Using Jenkins

Jenkins is at https://yourhost:444.

Jenkins home-directory can be found on the *host*:
```
/var/lib/docker/volumes/jenkins-compose_jenkins_home
```
When loggin into jenkins for the first time you are challanged to retrieve a file relative to this path.


## Using Sonarqube

The initial credentials are username: admin password: admin


## Using Selenium-Grid

Selenium-Grid is available inside the managed docker network (jenkins-compose_ci-net) at http://selenium-hub:4444/wd/hub.
It is also avaible on the host at localhost:4444

### Usage from outside
Create an SSH Tunnel mapping 127.0.0.1:4444 on the server to your local computer.

### Usage from a Jenkins-Pipeline
Keep in mind that while the Jenkins-Container is inside the managed network and can access Selenium, docker-build-agents used by Jenkins are not.
You need to tell Jenkins to explicitly bind the build-agent to the managed network Selenium is on.
```groovy
agent {
    dockerfile {
        args '--network jenkins-compose_ci-net'
    }
}
```
