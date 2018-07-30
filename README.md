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
- Selenium-Grid is at https://yourhost:4444/wd/hub
- Jenkins Slaves can connect via https://yourhost:5000


## Using Jenkins

Jenkins home-directory can be found on the *host*:
```
/var/lib/docker/volumes/jenkins-compose_jenkins_home
```
When loggin into jenkins for the first time you are challanged to retrieve a file relative to this path.


## Using Sonarqube

The initial credentials are username: admin password: admin


## Using Sonar-Grid

Services can controll Selenium-Grid via the provided url. While the access is secured through SSL keep in mind
that everyone can start tests *without* authentification. If this is not desired, remove seleniums section from nginx.conf.

*Keep in mind* however that when Jenkins starts a containerized agent it does not by default have access to the virtual network selenium is on.


