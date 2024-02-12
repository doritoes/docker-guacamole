# Docker Guacamole

**Disclaimer:** This work is based on the work of: https://github.com/oznu/docker-guacamole and heavily influenced by other forks

A Docker Container for [Apache Guacamole](https://guacamole.apache.org/), a client-less remote desktop gateway. It supports standard protocols like VNC, RDP, and SSH over HTML5.

This image will run on most platforms that support Docker. Goal is to test and confirm the following:
* Docker for Windows, Linux, Mac
* Synology DSM
* Raspberry Pi 3
* Raspberry Pi4 ARM64v8 on an 64bit OS

This container runs the guacamole web client, the guacd server and a postgres database.

[![Docker Pulls](https://img.shields.io/docker/pulls/doritoes/guacamole.svg)](https://hub.docker.com/r/doritoes/guacamole/)

## Status

**:warning: This project is a fork of oznu/docker-guacamole, which is archived and no longer supported**

**Latest**
* x64 build now passes and initial testing looks good
* postgresql 13
* VNC, RDP, and ssh have been tested (only)
* built-in authentication has been tested (only)
* tested Windows 10 and Ubuntu 20.04

**armhf**
* 32-bit Raspbian OS support, should work on Pi 2 and later
* build is in a "hack-me-up" state to get it to pass
* postgresql 9.6
* VNC and SSH have been tested
* RDP is not working currently; the dockerhub image oznu/armhf has working RDP; cloned at doritoes/guacamole:armhflegacy
* built-in authentication has been tested (only)
* tested on 32-bit OS on Pi 3B

**arm64**
* 64-bit Raspberry Pi OS support, should work on Pi 2B rev 1.2 and later (a22042	Q3 2016	2 Model B with BCM2837)
* postgresql 11
* VNC, RDP, and ssh have been tested (only)
* built-in authentication has been tested (only)
* tested on 64-bit Raspberry Pi OS on Pi 4B and Pi 3B

## Usage

### x86_64 / x64

```shell
docker run \
  -p 8080:8080 \
  -v </path/to/config>:/config \
  doritoes/guacamole
```

### Raspberry Pi

#### armhf / ARMv5 ARMv7 ARMv7

This image will also allow you to run [Apache Guacamole](https://guacamole.apache.org/) on a Raspberry Pi or other Docker-enabled ARMv5/6/7/8 devices by using the `armhf` tag.

Tested on Raspberry Pi 3B

Rebuilt but RDP not working due to upstream bug in freerdp
```shell
docker run \
  -p 8080:8080 \
  -v </path/to/config>:/config \
  doritoes/guacamole:armhf
```

Legacy image from oznu/guacamole with working RDP
```shell
docker run \
  -p 8080:8080 \
  -v </path/to/config>:/config \
  doritoes/guacamole:armhflegacy
```

#### ARM64v8

This image will also allow you to run [Apache Guacamole](https://guacamole.apache.org/) on a Raspberry Pi 2B or later, or other Docker-enabled ARMv5/6/7/8 devices by using the `arm64v8` tag.

Tested on Raspberry Pi 3B and 4B

```shell
docker run \
  -p 8080:8080 \
  -v </path/to/config>:/config \
  doritoes/guacamole:arm64v8
```

## Parameters

The parameters are split into two halves, separated by a colon, the left hand side representing the host and the right the container side.

* `-p 8080:8080` - Binds the service to port 8080 on the Docker host, **required**
* `-v /config` - The config and database location, **required**
* `-e EXTENSIONS` - See below for details.

## Enabling Extensions

Extensions can be enabled using the `-e EXTENSIONS` variable. Multiple extensions can be enabled using a comma separated list without spaces.

For example:

```shell
docker run \
  -p 8080:8080 \
  -v </path/to/config>:/config \
  -e "EXTENSIONS=auth-ldap,auth-duo"
  oznu/guacamole
```

Currently the available extensions are:

* auth-ldap - [LDAP Authentication](https://guacamole.apache.org/doc/gug/ldap-auth.html)
* auth-duo - [Duo two-factor authentication](https://guacamole.apache.org/doc/gug/duo-auth.html)
* auth-header - [HTTP header authentication](https://guacamole.apache.org/doc/gug/header-auth.html)
* auth-cas - [CAS Authentication](https://guacamole.apache.org/doc/gug/cas-auth.html)
* auth-openid - [OpenID Connect authentication](https://guacamole.apache.org/doc/gug/openid-auth.html)
* auth-totp - [TOTP two-factor authentication](https://guacamole.apache.org/doc/gug/totp-auth.html)
* auth-quickconnect - [Ad-hoc connections extension](https://guacamole.apache.org/doc/gug/adhoc-connections.html)

You should only enable the extensions you require, if an extensions is not configured correctly in the `guacamole.properties` file it may prevent the system from loading. See the [official documentation](https://guacamole.apache.org/doc/gug/) for more details.

## Default User

The default username is `guacadmin` with password `guacadmin`.

## Windows-based Docker Hosts

Mapped volumes behave differently when running Docker for Windows and you may encounter some issues with PostgreSQL file system permissions. To avoid these issues, and still retain your config between container upgrades and recreation, you can use the local volume driver, as shown in the `docker-compose.yml` example below. When using this setup be careful to gracefully stop the container or data may be lost.

```yml
version: "2"
services:
  guacamole:
    image: doritoes/guacamole
    container_name: guacamole
    volumes:
      - postgres:/config
    ports:
      - 8080:8080
volumes:
  postgres:
    driver: local
```

## License

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the [GNU General Public License](./LICENSE) for more details.
