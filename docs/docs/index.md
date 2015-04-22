---
title: Setting Up and Running Chronos
---

# Setting up and Running Chronos

## Quickstart

The [playa-mesos](https://github.com/mesosphere/playa-mesos) provides an easy way to try Mesos, Marathon and Chronos within a virtual machine using Vagrant.


## Requirements

These requirements are just to run Chronos. You will need additional packages to build Chronos from source (see the [Building from Source section](#build-from-source) below).

* [Apache Mesos][Mesos] 0.20.0+
* [Apache ZooKeeper][ZooKeeper]
* JDK 1.6+


## Install from Packages

Mesosphere provides builds for Mesos and Chronos for major Linux distributions and OS X on their [downloads page](http://mesosphere.com/downloads/).

## Install from Tarball

Use the latest tagged Chronos release with Mesos 0.20+ as follows:

```sh
curl -0 https://github.com/mesos/chronos/archive/2.3.3.tar.gz
tar xvf 2.3.3.tar.gz
```

<hr />

## <a name="build-from-source"></a>Building from Source

Follow these steps to install Chronos on Amazon Linux:

### Install Dependencies

#### Debian Linux:
    sudo apt-get install autoconf make gcc cpp patch python-dev git libtool default-jdk default-jdk-builddep default-jre gzip libghc-zlib-dev libcurl4-openssl-dev

#### Fedora Linux:
    sudo yum install autoconf make gcc gcc-c++ patch python-devel git libtool java-1.7.0-openjdk-devel zlib-devel libcurl-devel openssl-devel cyrus-sasl-devel

Make sure you're using Java 7: `sudo alternatives --config java`

### Build and Install Mesos

    git clone https://github.com/apache/mesos.git
    cd mesos/
    git checkout 
    export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.45.x86_64/
    ./bootstrap
    ./configure --with-webui --with-included-zookeeper --disable-perftools --enable-frame-pointers
    make
    sudo make install

### Build Chronos

Install [Node](http://nodejs.org/) first. On OSX, try `brew install node`.

Start up Zookeeper, Mesos master, and Mesos slave(s).  Then try

    export MESOS_NATIVE_LIBRARY=/usr/local/lib/libmesos.so
    git clone https://github.com/mesos/chronos.git
    cd chronos
    mvn package
    java -cp target/chronos*.jar org.apache.mesos.chronos.scheduler.Main --master zk://localhost:2181/mesos --zk_hosts localhost:2181

### Environment Variables Mesos Looks For

* `MESOS_NATIVE_LIBRARY`: Absolute path to the native mesos library. This is usually `/usr/local/lib/libmesos.so` on Linux and `/usr/local/lib/libmesos.dylib` on OSX.
* `MESOS_LAUNCHER_DIR`: Absolute path to the src subdirectory of your mesos build, such that the shell executor can be found (e.g. If mesos was built in `/Users/florian/airbnb_code/mesos/build` then the value for this variable would be `/Users/florian/airbnb_code/mesos/build/src`).
* `MESOS_KILLTREE`: Absolute path to the location of the `killtree.sh` script. (e.g. `/Users/florian/airbnb_code/mesos/src/scripts/killtree.sh`)

If you're using the installer script this should be setup for you.

<hr />

## Running Chronos

We've included some [example run scripts](#example-run-scripts), but the
basic syntax for launching chronos is:

    java -cp chronos.jar --master zk://127.0.0.1:2181/mesos --zk_hosts 127.0.0.1:2181

Please note that you need to have both Mesos and Zookeeper running for this to work!

For more information on configuration options, please see [Configuration]({{ site.baseurl }}/docs/configuration.html).

### Example Run Scripts

* Example [runit](http://smarden.org/runit/) run script

    [bin/run](https://github.com/mesos/chronos/blob/master/bin/run)

* Example local run script

    [bin/start-chronos.bash](https://github.com/mesos/chronos/blob/master/bin/start-chronos.bash)


[Mesos]: https://mesos.apache.org/ "Apache Mesos"
[Zookeeper]: https://zookeeper.apache.org/ "Apache ZooKeeper"
