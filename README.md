# s3gw
<a href="https://github.com/aquarist-labs/s3gw-core/blob/main/LICENSE"><img alt="GitHub license" src="https://img.shields.io/github/license/aquarist-labs/s3gw-core"></a>
<img alt="GitHub Workflow Status" src="https://img.shields.io/github/workflow/status/aquarist-labs/s3gw-core/Build%20Environment">
<a href="https://github.com/aquarist-labs/s3gw-core/issues"><img alt="GitHub issues" src="https://img.shields.io/github/issues/aquarist-labs/s3gw-core"></a>


An S3-compatible Gateway based on Ceph RGW, using a non-RADOS backend for
standalone usage.

This project provides the required infrastructure to build a container
able to run on a kubernetes cluster, providing S3-compatible endpoints to
applications.

# Table of Contents

- [s3gw](#s3gw)
- [Table of Contents](#table-of-contents)
- [Roadmap](#---roadmap)
- [Quickstart](#---quickstart)
  * [Helm chart](#helm-chart)
  * [Docker](#docker)
- [License](#license)
- [Further reading](#---further-reading)


# 🛣 Roadmap

![Roadmap](/assets/images/s3gw-roadmap.jpg)

The aim is to deliver a Minumum Viable Product (MVP). In a nutshell, an MVP seeks to collect the maximum amount of validated learning about our users in a short time.

Based on the user feedback we collect, we develop features that add up towards the goal of delivering the MVP. We're working on phases:

**Phase 1**
* Use SQLite as storage backend.
* Running the s3gw in a container.
* Be able to run basic S3 operations.
* Ability to deploy the S3 Gateway on a K8s or K3s cluster with.
* [Design mockups](https://www.figma.com/file/IeozuvvYlrKBs7qm030dyo/S3-Wireframe?node-id=0%3A1) for User management & an S3 explorer.

-----

**Phase 2**
* Basic S3 explorer UI
* Helm chart
* Define & start work on a file-based backend
* Implement CI/CD

-----

**Phase 3**
* File-based backend integration
* Increased support for S3 functional features
* S3 explorer UI
* User management UI
* tba

-----

**Project board**

We track our progress in this [Github project](https://github.com/orgs/aquarist-labs/projects/5/views/1) board.

# 🚀 Quickstart
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

# License

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


# 📖 Further reading
- [Developing the S3 Gateway](./docs/developing#----developing-the-s3-gateway)
- [How to build your own containers](./docs/build#---how-to-build-your-own-containers)
  * [Building the s3gw container image](./docs/build#building-the-s3gw-container-image)
  * [Building the s3gw-UI image](./docs/build#building-the-s3gw-ui-image)
- [Building a K3s & K8s environment running s3gw with Longhorn](./docs/env.md#building-a-k3s---k8s-environment-running-s3gw-with-longhorn)
  * [K3s Setup](./docs/env-k3s.md)
  * [K8s Setup](./docs/env-k8s.md)
- [S3 API compatibility table](./docs/s3-compatibility-table)
- [S3GW Repositories](#s3gw-repositories)
- [Contributing](./docs/contributing.md#contributing)
  * [Reporting an issue](./docs/contributing.md#reporting-an-issue)
  * [Discussion](./docs/contributing.md#discussion)
