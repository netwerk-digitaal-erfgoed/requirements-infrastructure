# Requirements for NDE application infrastructure

* [Introduction](#introduction)
* [Principles](#principles)
* [Definitions](#definitions)
* [Requirements](#requirements)

## Introduction

This document describes requirements for hosting applications that have been developed by Netwerk Digitaal Erfgoed (NDE).
This document is aimed at organisations that would like to take ownership of (‘borgen’) NDE Bruikbaar applications.

The requirements are based on modern DevOps best practices, including [Twelve-Factor](https://12factor.net).
They have inspired the [current NDE infrastructure](https://github.com/netwerk-digitaal-erfgoed/infrastructure).
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
    <dd>The set of components that enable serving applications to the public. Functions include not just hosting but also continuous integration, deployment and monitoring. These functions may be delivered by a single component or be spread over multiple components (for instance, GitHub Actions as a CI pipeline, hosting on a cluster).</dd>
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
        They are a form of lightweight virtualization, striking a good balance between isolation and performance.
        Containers are self-sufficient (without external dependencies) and uniform (they look the same on the outside).
        This makes them decoupled from infrastructure specifics (such as the OS used by the infrastructure provider).
    </dd>.
</dl>

## Requirements

### Must have

1. The infrastructure can run applications that are packaged as **OCI containers**. (SEP)
2. The infrastructure includes a container registry or can connect to an existing **container registry** (such as [GHCR](https://github.com/features/packages)) that built containers will be pushed to. (SEP)
3. The infrastructure runs **stateful applications** (such as triple stores).
4. The infrastructure can **configure applications** through [environment variables](https://12factor.net/config). (SEP)
5. The infrastructure **captures application logs** at [stdout](https://12factor.net/logs). (SEP)
6. The infrastructure **aggregates those logs** and makes them available through a command-line and/or web interface.
7. The infrastructure **automatically builds** the application source when commits are pushed to the application repository, producing in an OCI container build artefact. (AUTO)
8. The infrastructure **automatically runs application tests** when commits are pushed to the application repository. (AUTO)
9. The infrastructure **automatically deploys** new application versions to an acceptance and/or production environment. (AUTO)
10. The infrastructure is **highly available** corresponding to the infrastructure supplier’s SLA:
   at the very least there is no single point of failure and all components are redundant. (REL)
11. The infrastructure exposes applications at **public HTTPS web endpoints**.
12. The infrastructure provisions and renews **TLS certificates** for the web endpoints. (REL)
13. The infrastructure **backs up** all application data at an agreed upon interval and has restore functionality. (REL)
14. The infrastructure is **GDPR-compliant**, for instance in the way it stores data.
15. Access to the infrastructure is **restricted** to authorized persons. (REL)

### Should have

16. The infrastructure supports **zero-downtime deployments** of applications ([Blue/Green](https://martinfowler.com/bliki/BlueGreenDeployment.html) or otherwise). (REL)
17. The infrastructure configuration is **declared in code**. Changes made to that code are automatically rolled out. (AUTO)
18. Authorized developers can **view the infrastructure code** and propose changes to it.
19. The infrastructure captures **metrics**. (REL)
20. The infrastructure supports **alerts** to be configured on log (and metrics) output, supporting notification channels such as e-mail and Slack. (REL)
21. The application repository is **open source** and allows third-party contributors to submit issues and propose changes.
22. The infrastructure automatically configures **DNS** for new hostnames. (AUTO)
23. The infrastructure **automatically scales** up and down within set limits when application usage requires it. (AUTO)

### Could have

24. The infrastructure has an optimized way of hosting **static files** (useful for static HTML/JavaScript applications).
