# The Official Fedora Repository Docker Files

## How to use

```
docker run -p8080:8080 --name=fcrepo fcrepo/fcrepo
```

The default configuration provides the following tomcat users:

Username | Role          | Default password | Description
-------- | ------------- | ---------------- | -----------
fedoraAdmin | fedoraAdmin | fedoraAdmin     | Has full privileges on fedora

If you need additional users or don't want to provide the password for fedoraAdmin via a environment variable, you can specify your own `tomcat-users.xml` file with the environment variable `TOMCAT_USERS_FILE`, e.g:

```
docker run -p8080:8080 -v/path/on/host/tomcat-users.xml:/tomcat-users.xml -e TOMCAT_USERS_FILE=/tomcat-users.xml --name=fcrepo fcrepo/fcrepo
```

By default the docker container writes all data to `/var/lib/fcrepo`. For persistent storage, this path should be declared as a volume, e.g:

```
docker run -p8080:8080 -v/path/on/host:/var/lib/fcrepo --name=fcrepo fcrepo/fcrepo
```

### Passing in configuration

Custom configuration can be passed into the docker container by using the following environment variables:

Variable | Default Value | Description
-------- | ------------- | -----------
`CATALINA_OPTS` | none |
`TOMCAT_USERS_FILE` | `$CATALINA_HOME/conf/tomcat-users.xml` | Specify a custom tomcat-users.xml file with e.g. additional users
`FEDORA_ADMIN_USERNAME` | `fedoraAdmin` | If using the default tomcat-users.xml file: specify a custom username for the user `fedoraAdmin`
`FEDORA_ADMIN_PASSWORD` | `fedoraAdmin` | If using the default tomcat-users.xml file: specify a custom password  for the FEDORA_ADMIN_USERNAME defined above
`LOGBACK_CONFIGURATIONFILE` | none | Specify a custom logback.configurationFile

Use the CATALINA_OPTS environment variable to pass in Java options (-Dname=value). You may also pass all your options via a properties vile
using the "fcrepo.config.file" option.  For example: 
```
docker run -p8080:8080 -v/path/on/local/machine/to/properties:/fcrepo.properties -e CATALINA_OPTS="-Dfcrepo.config.file=/fcrepo.properties" --name=fcrepo fcrepo/fcrepo
```

If you want direct access to fcrepo.home directory (without having to ssh into the container) you can do something like this:

```
echo "fcrepo.home=/fedora_home" > /path/to/fcrepo.properties
mkdir -p /path/to/fedora_home
docker run -p8080:8080 -v/path/to/fedora_home:/fedora_home -v/path/to/fcrepo.properties:/fcrepo.properties -e CATALINA_OPTS="-Dfcrepo.config.file=/fcrepo.properties" --name=fcrepo fcrepo/fcrepo
```

Now you should be able to list the contents of our container's fcrepo.home directory from the local file system like so:
```
ls -last /path/to/fedora_home
```


For a detailed explanation of the configuration options have a look at [Application Configuration](https://wiki.lyrasis.org/display/FEDORA6x/Application+Configuration) in the Lyrasis Wiki.

## Development

### Repository organization

This repository is used to manage docker images for the Fedora Commons from Version 5.x onwards. The Dockerfile for the latest release of each major version is managed on separate branch. 

- `5.x-maintenance`: latest release of version 5 (coming soon)
- `main`: latest development snapshot

 ### Building the docker image locally

To build the docker image from a locally built snapshot of fcrepo-webapp, pass the path to the fcrepo-webapp-*.war to the build script:

```
./build.sh /path/to/fcrepo-webapp-<version>.war
```

This will create a docker image fcrepo/fcrepo in your local docker repository.
