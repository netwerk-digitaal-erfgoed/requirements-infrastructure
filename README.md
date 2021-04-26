# Requirements for NDE application infrastructure

* [Introduction](#introduction)
* [Principles](#principles)
* [Definitions](#definitions)
* [Requirements](#requirements)

## Introduction

This document describes requirements for hosting applications that have been developed by Netwerk Digitaal Erfgoed (NDE).
This document is aimed at organisations that would like to take ownership of (‘borgen’) NDE Bruikbaar applications.

The requirements are based on modern DevOps best practices, including [Twelve-Factor](https://12factor.net).
The [current NDE infrastructure](https://github.com/netwerk-digitaal-erfgoed/infrastructure) is based on these requirements.
By joining us in following these practices,
organisations will help enable the longevity and adoption of the applications.

## Principles

Guiding principles of these requirements are:

### SEP

A clear separation between the application and the infrastructure it runs on. 
The application code should not contain infrastructure specifics and be portable so application instances can be hosted by multiple parties, including developers that want to run or debug the application locally.
The infrastructure, in turn, should not have to change to accommodate the application.

### AUTO 

Processes are automated, which strengthens transparency and prevents human error;
for example the application code is built automatically when commits are pushed to the application repository,

### REL

The infrastructure is reliable, which helps adoption of the applications.
Both software developers and end-users need reliable and performant services to get their work done.

## Definitions

<dl>
    <dt>Infrastructure</dt>
    <dd>The set of components that enable serving applications to the public.
      Functions include not just an execution environment but also continuous integration, deployment and monitoring.
      These functions may be delivered by a single component or be spread over multiple components (for instance, GitHub Actions as a CI pipeline, hosting on a cluster).
    </dd>
    <dt>Application</dt>
    <dd>Any software application developed by Netwerk Digitaal Erfgoed (NDE) including supporting services such as databases.</dd>
    <dt>Application repository</dt>
    <dd>The code repository that contains the application’s source code.</dd>
    <dt>Application tests</dt>
    <dd>A test suite that is included with the application.</dd>
    <dt>Application data</dt>
    <dd>Data that is produced by applications, for instance data stored in databases. Also called application state.</dd>
    <dt>Container</dt>
    <dd>A package that contains both the application and its dependencies, 
        standardized by the <a href="https://opencontainers.org">Open Container Initiative (OCI)</a>.
    </dd>
</dl>

## Requirements

### Must have

#### 1. The infrastructure can run applications that are packaged as _OCI containers_. ([SEP](#sep))

Containers are a form of lightweight virtualization, striking a good balance between isolation and performance.
Containers are self-sufficient (without external dependencies) and uniform (they look the same on the outside).
This makes them decoupled from infrastructure specifics (such as the OS used by the infrastructure provider).
The same container can be run on any Linux distribution as well as on a developer’s local Mac or Windows machine.

#### 2. The infrastructure connects to a **container registry** that built containers will be pushed to. ([SEP](#sep))

Built containers are stored in a container registry.
This can be an external registry, such as as [GHCR](https://github.com/features/packages)
or one provided by the infrastructure itself.

The built artifacts must be accessible not only to the infrastructure itself but also to outside collaborators,
who can then run those same containers locally.

#### 3. The infrastructure runs _stateful applications_ (such as triple stores).

#### 4. The infrastructure can _configure applications_ through environment variables. ([SEP](#sep))

Configuration values (such as database connection strings or API URLs) must be parameterized.
The best way to do so is with [environment variables](https://12factor.net/config), a language- and OS-agnostic standard.

#### 5. The infrastructure _captures application logs_ at stdout. ([SEP](#sep))

Traditionally, logs are written by applications to a log file or an API (such as Logstash) as dictated by the infrastructure.
This couples the application to the infrastructure it runs on.
To safeguard proper separation, the infrastructure must capture application logs at the [standard output stream](https://12factor.net/logs) (`stdout`). 

#### 6. The infrastructure _aggregates logs_ and makes them available through a command-line and/or web interface. ([REL](#rel))

To see what is going on in the running application (observability),
developers as well as operations must be able to search through logs efficiently.
This is especially relevant when multiple container instances of the application are running in parallel.

#### 7. The infrastructure _automatically builds_ the application. ([AUTO](#auto))

When commits are pushed to the application source code, the infrastructure must automatically (re)build the application,
producing an OCI container build artefact.

#### 8. The infrastructure _automatically runs application tests_ when commits are pushed to the application repository. ([AUTO](#auto))

Automated tests prevent regressions only when they are run automatically on a standardized environment.

#### 9. The infrastructure _automatically deploys_ new application versions to an acceptance and/or production environment. ([AUTO](#auto))

#### 10. The infrastructure is _highly available_ corresponding to the infrastructure supplier’s SLA ([REL](#rel))

At the very least there is no single point of failure and all components are redundant.

#### 11. The infrastructure exposes applications at _public HTTPS web endpoints_.

Traditionally, large parts of infrastructure are only accessible internally in the organization.
NDE applications, however, are meant for the public and therefore must be publicly accessible over HTTPS.

#### 12. The infrastructure provisions and renews _TLS certificates_ for the web endpoints. ([REL](#rel))

Users must never get a ‘certificate expired’ error in their browser,
so the infrastructure must check expirations and renew certificates automatically,
for instance using [Let’s Encrypt](https://letsencrypt.org).

#### 13. The infrastructure _backs up_ all application data at an agreed upon interval and has restore functionality. ([REL](#rel))

Data loss is unacceptable, especially when that data has been provided by users.

#### 14. The infrastructure is _GDPR-compliant_, for instance in the way it stores data.

#### 15. Access to the infrastructure is _restricted_ to authorized persons. ([REL](#rel))

### Should have

16. The infrastructure supports **zero-downtime deployments** of applications ([Blue/Green](https://martinfowler.com/bliki/BlueGreenDeployment.html) or otherwise). ([REL](#rel))
17. The infrastructure configuration is **declared in code**. Changes made to that code are automatically rolled out. ([AUTO](#auto))
18. Authorized developers can **view the infrastructure code** and propose changes to it.
19. The infrastructure captures **metrics**. ([REL](#rel))
20. The infrastructure supports **alerts** to be configured on log (and metrics) output, supporting notification channels such as e-mail and Slack. ([REL](#rel))
21. The application repository is **open source** and allows third-party contributors to submit issues and propose changes.
22. The infrastructure automatically configures **DNS** for new hostnames. ([AUTO](#auto))

#### 24. The infrastructure **automatically scales** up and down within set limits when application usage requires it. ([AUTO](#auto))

In the case of vertical scaling (scaling in and out) this requires load-balancing the requests to the instances.

### Could have

24. The infrastructure has an optimized way of hosting **static files** (useful for static HTML/JavaScript applications).
