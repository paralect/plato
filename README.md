# Plato

Plato is a collection of applications, services, tools and guidelines written and managed 
as a single unit. Plato provides both foundation for your project and reference implementation
of several applications used by Paralect. 

Plato is flexible but very responsible. We will support any Plato embraced framework, service or 
tool as long as you use it. We will provide you with upgrade guidelines when it is time to move forward.   

## Overview

1. Monorepo. All platforms and tech stacks inside one repo. 
2. Trunc based development. Branches only for releases (almost).
3. Git LFS for large files (like PSD, MP4, etc)
4. Paralect specific apps and tools are also part of Plato

## Common infrastructure services and tools

Here are some of the most vital services and tools that are required 
by any application. This list shows how tremendously broad
functionality of Plato is. But no needs to worry :) Today we have
access to well established open-source cluster and container management
solutions that provide implementation for many listed features.

1. Configuration management 
1. Cluster management
1. Service discovery 
1. Metrics collection, aggregation and analysis 
1. Logs collection, aggregation and analysis
1. Healthchecks 
1. Solutions for high-availability and high-scalability
1. Application deployment pipeline 
1. Different types of Queues (In-Memory, Kafka, RabbitMQ)
1. Task and event scheduler
1. Scalable execution of unit tests
1. Scalable execution of WebDriver tests
1. Scalable processing of cold data (Hadoop)
1. Scalable processing of stream data (Heron, Samza, Spark)
1. High-speed distributed interactive analytics (Drill, Impala, Presto, Druid)
1. Deployment to top IaaS (maybe PaaS) services: AWS, Azure, DigitalOcean etc.
1. _quite a bit more..._

Root system that needs to be selected is a cluster and/or container management platform. 
Couple of months ago, [DC/OS][2] project [was announced][1] as compilation of Mesos, Marathon and
Mesosphere Datacenter Operating System. Among partners are such companies as Microsoft, Cisco, 
Confluent, HP, Citrix, Autodesk etc. Today Mesos can run Kubernetes and YARN, but vise-versa is
not true. Mesos also integrates well with IaaS stacks, such as OpenStack, CloudStack, OpenShift etc.
Also, we already have experience with Mesos+Marathon in Paralect. This naturally leads to selection
of DC/OS / Mesos as foundation of Plato. Things can change, and when they will change we should not 
cry, but instead gradually adopt _next generation solution™_.


## Consumer-oriented products

Besides common infrastructure services and frameworks, Plato will 
consists of at least the following consumer-oriented products:

1. Paralect internal and external sites, apps, mobile apps, services and tools
2. Robomongo site and services

## Shared cluster for PaaS services

Some services are well consumable by any application, even when application
is not part of Plato. For example, scheduler that communicates over HTTP or 
image processing services, or grid computation, or WebDriver tests etc. 

It means that Plato should be always running and always available.

## Userland

Application specific logic is written in JavaScript. All Plato services, frameworks and 
tools are consumable via JavaScript API. 

1. Single JavaScript specification across all engines (V8, Nashorn, SpiderMonkey, Chakra, Nitro) and use cases (Browser, Desktop, Mobile, Server)
2. ES6 and ES7 (gradually upgraded)
3. Babel as transpiler
4. Flow as typechecker
5. React and React Native for UI 
6. Support development on OS X and Linux hosts. Windows if time and demand. 


[1]: https://mesosphere.com/blog/2016/04/19/open-source-dcos/
[2]: https://dcos.io/
