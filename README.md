# colcon-bundle [![Build Status](https://travis-ci.org/colcon/colcon-bundle.svg?branch=master)](https://travis-ci.org/colcon/colcon-bundle)

This code is in active development and **should not** be considered stable.

This package is a plugin to [colcon-core](https://github.com/colcon/colcon-core.git). It provides functionality to bundle a built
workspace. A bundle is a portable environment which can be moved to a different linux system and executed
as if the contents of the bundle were installed locally.

Currently, ROS Kinetic is supported by installing the `colcon-ros-bundle` package.

# Installation

In order to use `colcon-bundle` execute the following (if you do not have root privileges you will need to run the `pip3` commands with `sudo`):

```
sudo apt-get install python3-apt python3-pip
sudo pip3 install -U setuptools pip
sudo pip3 install colcon-ros-bundle
```

# Building a bundle with `colcon bundle`

To build your ROS workspace into a bundle execute:

`colcon bundle`

This will parse your dependencies, download apt and pip dependencies, install the dependencies into the bundle, and
then install your built workspace into the bundle. The final output is located at `bundle/output.tar`

## In Docker

The simplest way to get up and running without affecting your local development environment is to use Docker.

The contents of an example Dockerfile are below. Create the Dockerfile in your workspace and then execute:

`docker build -t colcon-docker .` 

Once your docker image is built you can then run it with your local workspace mounted into the container by executing `docker run -it -v <PATH_TO_WORKSPACE>:/workspace colcon-docker bash`. Once inside the docker container `/workspace` will be bound to your local directory.

### Dockerfile

```
FROM ros:kinetic

RUN apt-get update && apt-get install -y python3-pip python3-apt

RUN pip3 install -U setuptools pip
RUN pip3 install colcon-ros-bundle
```

# Bundle Usage

When `colcon bundle` is executed in a ROS workspace it will create `bundle/output.tar` that follows the specification located [here](BUNDLE_FORMAT.md). 

A bundle is an entire application. In order to execute inside the bundle context follow the following steps:

1. Extract the main archive.
1. Extract `metadata.tar.gz` and look at `overlays.json`.
1. Extract each overlay listed in `overlays.json`.
1. In order execute `BUNDLE_CURRENT_PREFIX=<path to extracted overlay> source <path to extracted overlay>/setup.sh`
1. The bundle is now activated in your shell's environment.

# Package Blacklist

When we create the bundle we choose not to include certain packages that are included by default in most
Linux distributions. To create this blacklist for Ubuntu `apt list --installed | sed 's/^\(.*\)\/.*$/\1/'` was run on a base ubuntu:xenial container.

You can override this blacklist by using the `--apt-package-blacklist` argument.

# `colcon-bundle` Development

See [DEVELOPMENT.md](DEVELOPMENT.md)
