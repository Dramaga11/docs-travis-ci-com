---
title: Build a Java project
layout: en

---


<aside markdown="block" class="ataglance">

| Java                         | Default                                                                                                              |
|:-----------------------------|:---------------------------------------------------------------------------------------------------------------------|
| Default `install`            | [Gradle](#gradle-dependency-management), [Maven](#maven-dependency-management), [Ant](#ant-dependency-management )   |
| Default `script`             | [Gradle](#gradle-default-script-command), [Maven](#maven-default-script-command), [Ant](#ant-default-script-command) |
| [Matrix keys](#build-matrix) | `jdk`, `env`                                                                                                         |
| Support                      | [Travis CI](mailto:support@travis-ci.com)                                                                            |

Minimal example:

```yaml
  language: java
```
{: data-file=".travis.yml"}
</aside>

{{ site.data.snippets.unix_note }}

The rest of this guide covers configuring Java projects in Travis CI. If you're
new to Travis CI, please read our [Onboarding](/user/onboarding/) and
[General Build configuration](/user/customizing-the-build/) guides first.

## Overview

The Travis CI environment contains various versions of OpenJDK,
Gradle, Maven, and Ant.

To use the Java environment, add the following to your `.travis.yml`:

```yaml
language: java
```
{: data-file=".travis.yml"}

## Maven Projects

### Maven Dependency Management

Before running the build, Travis CI installs dependencies:

```bash
mvn install -DskipTests=true -Dmaven.javadoc.skip=true -B -V
```

or if your project uses the `mvnw` wrapper script:

```bash
./mvnw install -DskipTests=true -Dmaven.javadoc.skip=true -B -V
```

> Note that the Travis CI build lifecycle and the Maven build lifecycle use similar
terminology for different build phases. For example, `install` in a Travis CI
build comes much earlier than `install` in the Maven build lifecycle. More details
can be found about the [Travis Build Lifecycle](/user/job-lifecycle/)
and the [Maven Build Lifecycle](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html).

### Maven Default Script Command

If your project has `pom.xml` file in the repository root but no `build.gradle`,
Travis CI builds your project with Maven 3:

```bash
mvn test -B
```

If your project also includes the `mvnw` wrapper script in the repository root,
Travis CI uses that instead:

```bash
./mvnw test -B
```

> The default command does not generate JavaDoc (`-Dmaven.javadoc.skip=true`).

To use a different `script` command, customize the [build step](/user/job-lifecycle/#customizing-the-build-phase).

## Gradle Projects

### Gradle Dependency Management

Before running the build, Travis CI installs dependencies:

```bash
gradle assemble
```

or

```bash
./gradlew assemble
```

To use a different `install` command, customize the [installation step](/user/job-lifecycle/#customizing-the-installation-phase).

### Gradle Default Script Command

If your project contains a `build.gradle` file in the repository root, Travis CI
builds your project with Gradle:

```bash
gradle check
```

If your project also includes the `gradlew` wrapper script in the repository
root, Travis CI uses that wrapper instead:

```bash
./gradlew check
```

To use a different `script` command, customize the [build step](/user/job-lifecycle/#customizing-the-build-phase).

### Caching

A peculiarity of dependency caching in Gradle means that to avoid uploading the
cache after every build you need to add the following lines to your
`.travis.yml`:

```yaml
before_cache:
  - rm -f  $HOME/.gradle/caches/modules-2/modules-2.lock
  - rm -fr $HOME/.gradle/caches/*/plugin-resolution/
cache:
  directories:
    - $HOME/.gradle/caches/
    - $HOME/.gradle/wrapper/
```
{: data-file=".travis.yml"}

> Note that if you use Gradle with `sudo` (i.e. `sudo ./gradlew assemble`), the caching configuration above will have no effect, since the depencencies will be in `/root/.gradle` which the `travis` user account does not have write access to.

## Ant Projects

### Ant Dependency Management

Because there is no single standard way of installing project dependencies with
Ant, you need to specify the exact command to run using `install:` key in your
`.travis.yml`, for example:

```yaml
language: java
install: ant deps
```
{: data-file=".travis.yml"}

### Ant Default Script Command

If Travis CI does not detect Maven or Gradle files it runs Ant:

```bash
ant test
```

To use a different `script` command, customize the [build step](/user/job-lifecycle/#customizing-the-build-phase).

### Use Ant on Ubuntu Xenial (16.04)

Unfortunately, `ant` currently doesn't come pre-installed on our Xenial image. You'll need to install it manually by adding the following recipe to your .travis.yml file:

```yaml
dist: xenial
language: java
addons:
  apt:
    packages:
      - ant
```
{: data-file=".travis.yml"}


The list of available JVMs for different dists are at:

  * [JDKs installed for **Noble**](/user/reference/noble/#jvm-clojure-groovy-java-scala-support)
  * [JDKs installed for **Jammy**](/user/reference/jammy/#jvm-clojure-groovy-java-scala-support)
  * [JDKs installed for **Focal**](/user/reference/focal/#jvm-clojure-groovy-java-scala-support)
  * [JDKs installed for **Bionic**](/user/reference/bionic/#jvm-clojure-groovy-java-scala-support)
  * [JDKs installed for **Xenial**](/user/reference/xenial/#jvm-clojure-groovy-java-scala-support)
  * [JDKs installed for **Trusty**](/user/reference/trusty/#jvm-clojure-groovy-java-scala-images)
  * [JDKs installed for **Precise**](/user/reference/precise/#jvm-clojure-groovy-java-scala-vm-images)

### Switch JDKs (Java 8 and lower) within one Job

If your build needs to switch JDKs (Java 8 and below) during a job, you can do so with
[`jdk_switcher`](https://github.com/michaelklishin/jdk_switcher#what-jdk-switcher-is).

```yaml
script:
  - jdk_switcher use openjdk8
  - # do stuff with open Java 8
```
{: data-file=".travis.yml"}

Use of `jdk_switcher` also updates `$JAVA_HOME` appropriately.

## Use Java 10 and higher

> Take note that `oraclejdk10` is EOL since October 2018 and as such it's not supported anymore on Travis CI.
> See [https://www.oracle.com/technetwork/java/javase/eol-135779.html](https://www.oracle.com/technetwork/java/javase/eol-135779.html){: data-proofer-ignore=""}.
>
> `openjdk` is now the default jdk available on our VMs as `install-jdk` no longer installs `oraclejdk`. Please see this [Github Issue](https://github.com/sormuras/bach/issues/56) for context.

### Switch JDKs (to Java 10 and higher) within one Job

If your build needs to switch JDKs (Java 10 and up) during a job, you can do so with
[`install-jdk.sh`](https://sormuras.github.io/blog/2017-12-08-install-jdk-on-travis.html).

```yaml
jdk: openjdk10
script:
  - jdk_switcher use openjdk10
  - # do stuff with OpenJDK 10
  - wget https://github.com/sormuras/bach/raw/master/install-jdk.sh
  - chmod +x $TRAVIS_BUILD_DIR/install-jdk.sh
  - export JAVA_HOME=$HOME/openjdk11
  - $TRAVIS_BUILD_DIR/install-jdk.sh -F 11 --target $JAVA_HOME
  - # do stuff with open OpenJDK 11
```
{: data-file=".travis.yml"}

## Current JDK providers

Currently, our builds are using Bellsoft and Adoptium JDK providers - they are switched based on the distribution and architecture you are using in your build. Additionally, we've added IBM's Semeru JDK - to use it, instead of using jdk: jdkX or jdk: openjdkX syntax, simply use jdk: semeruX like here:
```yaml
language: java
jdk: semeru11
```
{: data-file=".travis.yml"}

Available semeru JDKs for AMD, S390X, PPC64LE, and ARM architectures: 8, 11, 16, 17, 18, 19, 20, 21, 22

## Examples

- [JRuby](https://github.com/jruby/jruby/blob/master/.travis.yml)
- [Riak Java client](https://github.com/basho/riak-java-client/blob/master/.travis.yml)
- [Cucumber JVM](https://github.com/cucumber/cucumber-jvm/blob/master/.travis.yml)
- [Symfony 2 Eclipse plugin](https://github.com/pulse00/Symfony-2-Eclipse-Plugin/blob/master/.travis.yml)
- [RESThub](https://github.com/resthub/resthub-spring-stack/blob/master/.travis.yml)
- [Joni](https://github.com/jruby/joni/blob/master/.travis.yml), JRuby's regular expression implementation

## Build Config Reference

You can find more information on the build config format for [Java](https://config.travis-ci.com/ref/language/java) in our [Travis CI Build Config Reference](https://config.travis-ci.com/).
