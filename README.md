# travis-docker
[![Build Status](https://img.shields.io/travis/moul/travis-docker.svg)](https://travis-ci.org/moul/travis-docker) [![GuardRails badge](https://badges.production.guardrails.io/moul/travis-docker.svg)](https://www.guardrails.io)

Running Docker in a Travis CI build.

---

### [Travis now officially supports Docker !](https://docs.travis-ci.com/user/docker/)

This project is now deprecated, but the resources may be useful:

* as an example to create Docker workspaces on environments with constraints
* to use a specific version of Docker or a specific daemon configuration on Travis

---

![](https://github.com/moul/travis-docker/raw/master/assets/logo.png)

`./run` script will run commands in a user-land linux with `docker` and
`docker-compose` and pass back the exit code.


## *.travis.yml* example

Simple

```yaml
sudo: true

install:
  - curl -sLo - http://j.mp/install-travis-docker | sh -xe

script:
  - ./run 'docker build -t test . && docker run test'
```

Advanced

```yaml
sudo: true                  # Required to install packages

env:
  global:
    - DOCKER_VERSION=1.7.1  # Install Docker version 1.7.1
    - DOCKER_VERSION=get.docker.com  # Install Docker using get.docker.com (may break)

    - BRANCH=stable         # Use 'stable' branch
    - BRANCH=develop        # Use 'develop' branch

    - QUIET=1               # Less verbose logging
    - UML_FIG=0             # Don't install fig
    - UML_DOCKERCOMPOSE=1   # Install docker-compose

install:
  # Install https://github.com/moul/travis-docker toolchain
  - curl -sLo - http://j.mp/install-travis-docker | sh -xe

script:
  - ./run docker run busybox ls -la
  - ./run docker run busybox ls -la /non-existing-dir
  - ./run 'docker-compose up -d blog && docker ps && date'
  - ./run 'apt-get install git && git clone myrepo && cd myrepo && docker build -t myimage .'
  - ./run 'docker build -t test . && docker run test'
  - ./run 'ruby my_app_that_communicates_with_docker_socket.rb'
```

You can find more examples on [travis-docker-example](https://github.com/moul/travis-docker-example).


## Environment variables

* `DOCKER_VERSION=1.8.0-rc2`, default is `1.7.1`, available values are all the
  Docker releases + `get.docker.com` for docker's install script
* `DOCKER_STORAGE_DRIVER=aufs`, default is `devicemapper`, available values are
  `aufs`, `btrfs`, `devicemapper`, `vfs`, `overlay`
* `UML_DOCKERCOMPOSE=0`, do not install `docker-compose`
* `UML_FIG=0`, do not install `fig`
* `QUIET=1`, be less verbose

## sudo requirement

As you probably noticed, the examples above contained the line `sudo: true`.
Travis is [moving towards a new infrastructure](https://docs.travis-ci.com/user/migrating-from-legacy/)
where `sudo` is not supported (yet?).
That line ensures that your project will state that your project requires `sudo`
(as travis-docker does) and should therefore not be run on the new infrastructure.
For existing projects, which have a history of runs on Travis,
the old infrastructure might get used even without that line.
So it *might* be unneccessary for your project.
But since the line also affects people forking your project,
you probably shouldn't rely on that compatibility,
but instead make your requirements explicit.

---

## Changelog

### v1.1.0 (2015-07-30) (`BRANCH=v1.1.0` or `BRANCH=stable`)

* The Docker version can now be choose
* Improved documentation (sudo, yaml)
* Improved install script

usage: .travis.yml

```yaml
env:
  global:
    - BRANCH=v1.1.0

sudo: true

install:
  - curl -sLo - http://j.mp/install-travis-docker | sh -xe

script:
  - ./run 'docker build -t test . && docker run test'
  - ./run 'docker-compose up -d blog && docker ps'
```

[Full changelog](https://github.com/moul/travis-docker/compare/v1.0.0...v1.1.0)

### v1.0.0 (2015-06-02) (`BRANCH=v1.0.0`)

Update (2015-07-30): this version is now unstable due to
[Docker's new install script](http://blog.docker.com/2015/07/new-apt-and-yum-repos/).

First stable version

usage: .travis.yml

```yaml
env:
  global:
    - BRANCH=v1.0.0
    - QUIET=1

sudo: true

install:
  - curl -sLo - http://j.mp/install-travis-docker | sh -xe

script:
  - ./run 'docker build -t test . && docker run test'
  - ./run 'docker-compose up -d blog && docker ps'
```

* First "stable" version
* /var/lib/docker is mounted as tmpfs (limited to 2Gib)
* No persistency between `./run` calls
* Optional packages `fig` and `docker-compose` are installed by default and
  can be skiped with `UML_FIG=0` `UML_DOCKERCOMPOSE=0`
* Verbose mode is enabled by default and can be disabled with `QUIET=1`
* Default storage driver is `aufs` and can be changed using
  `DOCKER_STORAGE_DRIVER={devicemapper,aufs,btrfs,vfs,overlay}`


### v0.0.0 (2015-01-18)

Proof of concept


---

## Projects using [travis-docker](https://github.com/moul/travis-docker)

- https://github.com/andrewsomething/fabric-package-management
- https://github.com/CloudSlang/cloud-slang-content
- https://github.com/eliotjordan/rails-docker-test
- https://github.com/HanXHX/ansible-debian-dotdeb
- https://github.com/ir4y/fabric-scripts
- https://github.com/moul/docker-icecast
- https://github.com/moul/travis-docker-example
- https://github.com/pathwar/core
- https://github.com/pathwar/level-helloworld
- https://github.com/rporrini/abstat-inf
- https://github.com/scaleway/kernel-tools
- https://github.com/stevenalexander/docker-nginx-dropwizard
- https://github.com/theodi/chef-certificates
- https://github.com/Webtrends/wookiee
- https://github.com/William-Yeh/ansible-nginx
- https://github.com/William-Yeh/ansible-nodejs
- https://github.com/William-Yeh/docker-ansible
- https://github.com/William-Yeh/docker-dash
- https://github.com/William-Yeh/docker-mini
- https://github.com/William-Yeh/docker-wrk

## License

MIT
