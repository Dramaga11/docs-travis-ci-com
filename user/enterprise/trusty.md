---
title: Trusty Build Containers for Enterprise
layout: en_enterprise

---

> Please note that support for Trusty build environment is discontinued for Travis CI Enterprise. This is a **legacy** document left for reference.

## System Setup

**Platform Requirements**: To use the Trusty build containers, the Travis CI installation must be at 2.1.9 or higher. Please be sure to [upgrade](/user/enterprise/upgrading/), if needed, before getting started.

**Worker Requirements**:

We recommend using a machine with 8 vCPUs and 15 GB of memory, and at least 40 GB of disk space. If you're using AWS, this will be their c4.2xlarge instance type. Also, you'll want to run Ubuntu 16.04 or later. Port 22 must be open for SSH during installation and operation.

> _Precise build containers and Trusty build containers must be on different instances_. To run Precise and Trusty builds, at least two worker instances are required.

## Install with Travis CI Enterprise 2.2 and higher

Once a worker instance is up and running, `travis-worker` can be installed as follows:

```
curl -sSL -o /tmp/installer.sh https://raw.githubusercontent.com/travis-ci/travis-enterprise-worker-installers/master/installer.sh

sudo bash /tmp/installer.sh \
--travis_enterprise_host="[travis.yourhost.com]" \
--travis_enterprise_security_token="[RabbitMQ Password/Enterprise Security Token]"
```

This installer uses Docker's `aufs` storage driver. If you have any questions or concerns, please [get in touch with us](mailto: enterprise@travis-ci.com?subject=Precise%20Workers) to discuss alternatives.


## Install with Travis CI Enterprise 2.1.9 and higher

The Travis CI Enterprise 2.1 series has the [Precise [Legacy]](/user/enterprise/precise/) as its default worker. However, starting with version 2.1.9+, it is possible to use Trusty build environments, assuming the feature flags are set. Otherwise, the installation process is very similar to the Enterprise 2.2 series.

### Enable the Trusty Beta Feature Flag

1. SSH into the platform machine.
2. Run `travis console`.
3. Then run `Travis::Features.enable_for_all(:template_selection); Travis::Features.enable_for_all(:multi_os)`
4. Type in `exit` to leave the console.
5. Disconnect from the Travis Enterprise platform machine.

### Install the travis-worker

Once a worker instance is up and running, `travis-worker` can be installed as follows:

```
curl -sSL -o /tmp/installer.sh https://raw.githubusercontent.com/travis-ci/travis-enterprise-worker-installers/master/installer.sh

sudo bash /tmp/installer.sh \
--travis_enterprise_host="[travis.yourhost.com]" \
--travis_enterprise_security_token="[RabbitMQ Password/Enterprise Security Token]"
```

This installer uses Docker's `aufs` storage driver. If you have any questions or concerns, please [get in touch with us](mailto: enterprise@travis-ci.com?subject=Precise%20Workers) to discuss alternatives.

### Run builds on Trusty on Travis CI Enterprise 2.1.9 and higher

To run builds on a worker with Trusty images, please add `dist: trusty` to your `.travis.yml`. If that key is not present in your project's `.travis.yml`, the build will be routed to the default (Precise) build environments instead.

## Restart travis-worker

After installation, or when configuration changes are applied to the worker, restart the worker as follows:

`sudo service travis-worker restart`

Worker configuration changes are applied on start.

## Contact Enterprise Support

{{ site.data.snippets.contact_enterprise_support }}
