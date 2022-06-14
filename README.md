# s3gw
<a href="https://github.com/aquarist-labs/s3gw-core/blob/main/LICENSE"><img alt="GitHub license" src="https://img.shields.io/github/license/aquarist-labs/s3gw-core"></a>
<img alt="GitHub Workflow Status" src="https://img.shields.io/github/workflow/status/aquarist-labs/s3gw-core/Build%20Environment">
<a href="https://github.com/aquarist-labs/s3gw-core/issues"><img alt="GitHub issues" src="https://img.shields.io/github/issues/aquarist-labs/s3gw-core"></a>


We're building an S3-compatible Gateway based on Ceph RGW, using a non-RADOS backend for
standalone usage.

This project will provide the required infrastructure to build a container
able to run on a kubernetes cluster, providing S3-compatible endpoints to
applications.

# Table of Contents

- [Roadmap](#---roadmap)
  * [Project board](#project-board)
- [Quick start](#---quick-start)
  * [Helm chart](#helm-chart)
  * [Docker](#docker)
- [Developing the S3 Gateway](#----developing-the-s3-gateway)
  * [Introduction](#introduction)
  * [Requirements](#requirements)
  * [Running the Gateway](#running-the-gateway)
- [Containers](#---containers)
  * [Building the s3gw container image](#building-the-s3gw-container-image)
  * [Building the s3gw-UI image](#building-the-s3gw-ui-image)
  * [Building and running a complete environment](#building-and-running-a-complete-environment)
- [Repositories](#repositories)
- [Reporting an issue](#reporting-an-issue)
- [Community](#---community)
- [License](#license)


# 🚧 Roadmap

After delivering an initial proof of concept, we're currently focusing on developing a Minimum Viable Product. Once we achieve this deliverable and depending on the user feedback being positive, amongst other factors, we'll proceed to the next phase of development.

The image below represents our current roadmap.

![S3GW Roadmap](/assets/images/s3gw-roadmap.jpg)

## Project board
We also keep track of our items in an Aquarist Labs organization dedicated
[project](https://github.com/orgs/aquarist-labs/projects/5/views/1).

# 🚀 Quick start
## Helm chart
An easy way to deploy the S3 Gateway on your Kubernetes cluster is via a Helm chart.
We've created a dedicated repository for it, which can be found [here](https://github.com/aquarist-labs/s3gw-charts).

## Docker
```
docker pull ghcr.io/aquarist-labs/s3gw:latest
```

In order to run the Docker container:

```
docker run -p 7480:7480 localhost/s3gw
```

For more information on building and running a container, please read our [guide](./build/).

# 🖥️ Developing the S3 Gateway

## Introduction

Given we are still setting up the project, figuring out requirements, and
specific details about direction, we are dedicating most of our efforts to
testing Ceph's RGW as a standalone daemon using a non-RADOS storage backend.

The backend in question is called `dbstore`, backed by a SQLite database, and
is currently provided by RGW.

In order to ensure we all test from the same point in time, we have a forked
version of the latest development version of Ceph, which can be found
[here](https://github.com/aquarist-labs/ceph.git). We are working using the
[`s3gw` branch](https://github.com/aquarist-labs/ceph/tree/s3gw) as our base of
reference.

Keep in mind that this development branch will likely closely follow Ceph's
upstream main development branch, and is bound to change over time. We intend
to contribute whatever patches we come up with to the original project, thus
we need to keep up with its ever evolving state.

## Requirements

We are relying on built Ceph sources to test RGW. We don't have a particular
preference on how one achieves this. Some of us rely on containers to build
these sources, while others rely on whatever OS they have on their local
machines to do so. Eventually we intend to standardize how we obtain the
RGW binary, but that's not in our immediate plans.

If one is new to Ceph development, the best way to find out how to build
these sources is to refer to the
[original documentation](https://docs.ceph.com/en/pacific/install/build-ceph/#id1).

Because we are in a fast development effort at the moment, we have chosen to
apply patches needed to make our endeavour work on our own fork of the Ceph
repository. This allows us fiddle with the Ceph source while experimenting,
without polluting the upstream Ceph repository. We do intend to upstream any
patches that make sense though.

That said, we have the `aquarist-labs/ceph` repository as a requirement for
this project. We can't guarantee that our instructions, or the project as a
whole, will work flawlessly with the original Ceph project from `ceph/ceph`.

## Running the Gateway

One should be able to get a standalone Gateway running following these steps:

```
cd build/
mkdir -p dev/rgw.foo
bin/radosgw -i foo -d --no-mon-config --debug-rgw 15 \
  --rgw-backend-store dbstore \
  --rgw-data $(pwd)/dev/rgw.foo \
  --run-dir $(pwd)/dev/rgw.foo
```

Once the daemon is running, and outputting its logs to the terminal, one can
start issuing commands to the daemon. We rely on `s3cmd`, which can be found
on [github](https://github.com/s3tools/s3cmd) or obtained through `pip`.

`s3cmd` will require to be configured to talk to RGW. This can be achieved by
first running `s3cmd -c $(pwd)/.s3cfg --configure`. By default, the configuration
file would be put under the user's home directory, but for our testing purposes
it might be better to place it somewhere less intrusive.

During the interactive configuration a few things will be asked, and we
recommend using these answers unless one's deployment is different, in which
case these will need to be properly adapted.

```
  Access Key: 0555b35654ad1656d804
  Secret Key: h7GhxuBLTrlhVUyxSPUKUV8r/2EI4ngqJxD7iBdBYLhwluN30JaT3Q==
  Default Region: US
  S3 Endpoint: 127.0.0.1:7480
  DNS-style bucket+hostname:port template for accessing a bucket: 127.0.0.1:7480/%(bucket)
  Encryption password: ****
  Path to GPG program: /usr/bin/gpg
  Use HTTPS protocol: False
  HTTP Proxy server name:
  HTTP Proxy server port: 0
```

Please note that both the `Access Key` and the `Secret Key` need to be copied
verbatim. Unfortunately, at this time, the `dbstore` backend statically creates
an initial user using these values.

Should the configuration be correct, one will then be able to issue commands
against the running RGW. E.g., `s3cmd mb s3://foo`, to create a new bucket.

# 📦 Containers
## Building the s3gw container image
You can refer to the [build](./build/) section to understand how to build the `s3gw` container image.

## Building the s3gw-UI image

You can refer to the [build-ui](./build-ui/) section to build the `s3gw-UI` image.

## Building and running a complete environment

You can refer to the [environment](./env/) section to build a fully provisioned Kubernetes cluster.

# S3 API compatibility
The following table describes the support status for current Amazon S3 functional features:

| Feature                   | Status  |
|---------------------------|---------|
| List Buckets              | ✅       |
| Delete Bucket             | ✅       |
| Create Bucket             | ✅       |
| Bucket Lifecycle          | ✅       |
| Bucket Replication        | Partial |
| Policy (Buckets, Objects) | ✅       |
| Bucket Website            | ✅       |
| Bucket ACLs (Get, Put)    | ✅       |
| Bucket Location           | ✅       |
| Bucket Notification       | ✅       |
| Bucket Object Versions    | ✅       |
| Get Bucket Info (HEAD)    | ✅       |
| Bucket Request Payment    | ✅       |
| Put Object                | ✅       |
| Delete Object             | ✅       |
| Get Object                | ✅       |
| Object ACLs (Get, Put)    | ✅       |
| Get Object Info (HEAD)    | ✅       |
| POST Object               | ✅       |
| Copy Object               | ✅       |
| Multipart Uploads         | ✅       |
| Object Tagging            | ✅       |
| Bucket Tagging            | ✅       |
| Storage Class             | ✅       |


# Other information
## Repositories
The S3 Gateway includes the following projects:

* https://github.com/aquarist-labs/s3gw-core
* https://github.com/aquarist-labs/ceph
* https://github.com/aquarist-labs/s3gw-charts
* https://github.com/aquarist-labs/s3gw-status

## Reporting an issue

If one finds a problem, as one is bound to at this point in time, we encourage
everyone to check out our [issues list](https://github.com/aquarist-labs/s3gw-core/issues)
and either file a new issue if the observed behavior has not been reported
yet, or to contribute with further details to an existing issue.

## Discussion

You can join us in [Slack](https://join.slack.com/t/aquaristlabs/shared_invite/zt-nphn0jhg-QYKw__It8JPMkUR_sArOug).

## License

Licensed under the Apache License, Version 2.0 (the "License");
you may not use licensed files except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

or the LICENSE file in this repository.

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

