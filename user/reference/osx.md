---
title: The macOS Build Environment
layout: en
redirect_from:
  - /user/osx-ci-environment/
  - /user/workers/os-x-infrastructure/
---

### What This Guide Covers

This guide explains what packages, tools and settings are available in the
Travis macOS CI environment (often referred to as the “CI environment”).



## Overview

Travis CI runs builds in virtual machines that are snapshotted before each build
and rolled back at the end of it. This offers a number of benefits:

- Host OS is not affected by test suites
- No state persists between runs
- Passwordless sudo is available
- It is possible for test suites to install various services via `brew`
  and then customize the configuration of those services at the
  beginning of each build.

The environment available to test suites is known as the *Travis CI
environment*.

## Using macOS

To use our macOS build infrastructure, add the following to your `.travis.yml`:

```yaml
os: osx
```
{: data-file=".travis.yml"}

Travis CI also supports the [Ubuntu Linux Environment](/user/reference/linux/), [Windows Environment](/user/reference/windows/) and [FreeBSD Environment](/user/reference/freebsd/).

## macOS Version

Travis CI uses macOS 10.13 and Xcode 9.4.1 by default. You can use another version of macOS (and Xcode) by specifying the corresponding `osx_image` key from the following table:

<table>

<tr align="left"><th>osx_image value</th><th>Xcode version</th><th>Xcode build version</th><th>macOS version</th><th>JDK</th></tr>
{% for image in site.data.xcodes.osx_images %}
<tr>
  <td><code>osx_image: {{image.image}}</code>{% if image.default == true %}  <em>Default</em> {% endif %}</td>
  <td><a href="#xcode-{{image.xcode | downcase | remove:'.' | remove: '-'}}">Xcode {{ image.xcode_full_version }}</a></td>
  <td>{{ image.xcode_build_version}}</td>
  <td>macOS {{ image.osx_version}}</td>
  <td>{{image.jdk}}</td>
  </tr>
{% endfor %}
</table>

## Homebrew

Homebrew is installed and updated every time the virtual machines are updated.

> The [Travis Homebrew addon](/user/installing-dependencies/#installing-packages-on-macos) is the simplest, fastest and most reliable way to install dependencies.

The Homebrew addon correctly handles up-to-date, outdated, and missing packages. Manual Homebrew dependency scripts are error-prone, and we recommend against using them.

The Homebrew addon uses the Homebrew database on the build image by default, but can be configured to run `brew update` if needed.

## File System

VMs running macOS 10.13 use HFS+, VMs running macOS 10.14 and newer use APFS.


## JDK and macOS

Note the pre-installed JDK version (OracleJDK) for each image in the table below.
While Mac jobs can test against multiple JDK versions using the [`jdk` key](/user/languages/java/#testing-against-multiple-jdks),
macOS images up to `xcode9.3` can only switch up to Java 8, and images `xcode9.4` and later can switch to Java 10 (if pre-installed) and later.
In practical terms, if your Mac build requires Java 8 and below, use `xcode9.3` (or below); if your build requires Java 10
and later, use `xcode9.4` (or later).

<table>

<tr align="left"><th>osx_image value</th><th>Xcode version</th><th>macOS version</th><th>JDK</th></tr>
{% for image in site.data.xcodes.osx_images %}
<tr>
  <td><code>osx_image: {{image.image}}</code>{% if image.default == true %}  <em>Default</em> {% endif %}</td>
  <td><a href="#xcode-{{image.xcode | downcase | remove:'.' | remove: '-'}}">Xcode {{ image.xcode_full_version }}</a></td>
  <td>macOS {{ image.osx_version}}</td>
  <td>{{image.jdk}}</td>
  </tr>
{% endfor %}
</table>


## Compilers and Build toolchain

- automake
- clang
- cmake
- gcc
- maven
- mercurial
- pkg-config
- wget
- xctool

## Languages

- C
- C++
- Go
- Java
- Nodejs
- Python
- Ruby

## Runtimes

Every worker has at least one version of Go, Java, Python, Ruby and NodeJS to accommodate
projects that may need one of those runtimes during the build.

## Environment variables

- `CI=true`
- `TRAVIS=true`
- `USER=travis`
- `HOME=/Users/travis`

Additionally, Travis CI sets environment variables you can use in your build,
e.g.  to tag the build, or to run post-build deployments.

- `TRAVIS_BRANCH`:For builds not triggered by a pull request this is the
  name of the branch currently being built; whereas for builds triggered
  by a pull request this is the name of the branch targeted by the pull
  request (in many cases this will be `master`).
- `TRAVIS_BUILD_DIR`: The absolute path to the directory where the repository
  being built has been copied on the worker.
- `TRAVIS_BUILD_ID`: The id of the current build that Travis CI uses internally.
- `TRAVIS_BUILD_NUMBER`: The number of the current build (for example, "4").
- `TRAVIS_COMMIT`: The commit that the current build is testing.
- `TRAVIS_COMMIT_RANGE`: The range of commits that were included in the push
  or pull request. (Note that this is empty for builds triggered by the initial commit of a new branch.)
- `TRAVIS_JOB_ID`: The id of the current job that Travis CI uses internally.
- `TRAVIS_JOB_NUMBER`: The number of the current job (for example, "4.1").
- `TRAVIS_PULL_REQUEST`: The pull request number if the current job is a pull
  request, "false" if it's not a pull request.
- `TRAVIS_SECURE_ENV_VARS`: Whether or not secure environment vars are being
  used. This value is either "true" or "false".
- `TRAVIS_REPO_SLUG`: The slug (in form: `owner_name/repo_name`) of the
  repository currently being built. (for example, "travis-ci/travis-build").
- `TRAVIS_OS_NAME`: On multi-OS builds, this value indicates the platform the job is running on.
  Values are `linux` and `osx` currently, to be extended in the future.
- `TRAVIS_TAG`: If the current build is for a git tag, this variable is set to the tag's name.

## Ruby versions/implementations

Default macOS Ruby (depends on macOS version) -- You need to use `sudo` to install gems with this Ruby and you can also use the [pre-compiled Ruby binaries](https://rubies.travis-ci.org/) we made available.

Rubies are built using [RVM](http://rvm.io/) that is installed per-user.

## Gems in the global gem set

- bundler
- rake
- cocoapods

## Python related tools

- pyenv (via homebrew)
- virtualenv (via pip)
- numpy (via pip)
- scipy (via pip)
- tox (via pip)

## Xcode version

Xcode 9.4.1 is installed with all available simulators and SDKs.
Command Line Tools are also installed.

{% for image in site.data.xcodes.osx_images %}

### Xcode {{image.xcode}}

Xcode {{ image.xcode_full_version }} is available by adding `osx_image: {{ image.image }}` to your .travis.yml.

{% if image.default == true %} -- **Default when no other `osx_image:` is specified** {% endif %}

{% if image.image_note != nil %}
{{image.image_note}}
{% endif %}

Our Xcode {{ image.xcode_full_version }} images have the following SDKs preinstalled:

{% for sdk in image.sdks %}

- {{ sdk }}{% endfor %}

The Xcode {{ image.xcode_full_version }} image also comes with the following simulators:
{% for simulator in image.simulators %}

- {{ simulator }}{% endfor %}

{% endfor %}
