# 🐒 Plato

_This is a draft of the plan for Plato. Several technologies proposed and more to come
soon._

Plato is a collection of applications, services, tools and guidelines written and managed 
as a single unit. Plato provides both foundation for your project and reference implementation
of several applications used by Paralect. 

Plato is commited to be modern and up to date. Bleeding edge technologies are also welcome,
as long as reliability and stability are not sacrified. Because landscape of tools, frameworks and
technologies constantly changes and evolves, someone should track and prepare the way for the changes.
This is the role that Plato will try to play. When it is time to move forward, you will receive 
upgrade guidelines and all required documentation.

Plato serves as a communication channel and experimentation playground for all Paralect teammates.
Any proposal, suggestion, fix or implementation are welcome!

## Overview

1. Monorepo. All platforms and tech stacks inside one repo. 
2. Trunc based development. Branches only for releases (almost).
3. Git LFS for large files (like PSD, MP4, etc)
4. Third party software can be both open source or proprietary
5. Third party software can be both hosted or available in the cloud
6. Paralect specific apps and tools are also part of Plato

## Proposal

_This is a preliminary proposal that is changing every day. Share your 
opinion or vote for the particualar technology._ 

In two sentences: _"Google architecture for infrastructure. Facebook architecture for applications."_

🔹**Core technologies**

1. **Kubernetes** for Cluster and Container Management
2. **CoreOS** as Container Operating System
3. **Docker** as Container Runtime

🔹**Infrastructure development**

1. **Go** as preferable language, if makes sense. Any choice is permitted. 

🔹**Application development**

1. **Single JavaScript Specification** across all engines (V8, Nashorn, SpiderMonkey, Chakra, Nitro) and use cases (Browser, Desktop, Mobile, Server)
2. **ES6/ES7** as Language Dialect (TODO: specify more strictly in a form of Babel config flags)
3. **Babel** as Transpiler
4. **Flow** as Typechecker
5. **React** and **React Native** for UI 
6. **Linux** and **OS X** as supported development platforms from day one. Windows in a few months.

🐥 Nothing is finally selected! This is a proposal from which to start investigations. Share 
your ideas!

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
1. Health checking
1. Load balancing
1. Replication of application services
1. Resource usage monitoring
1. High availability and high scalability for all movable parts 
1. Application deployment pipeline 
1. Different types of Queues (In-Memory, Kafka, RabbitMQ)
1. Task and event scheduler
1. Scalable execution of unit tests
1. Scalable execution of WebDriver tests
1. Load and performance testing
1. Scalable processing of cold data (Hadoop)
1. Scalable processing of stream data (Heron, Samza, Spark)
1. High-speed distributed interactive analytics (Drill, Impala, Presto, Druid)
1. Deployment to top IaaS (maybe PaaS) services: AWS, Azure, DigitalOcean etc.
1. Private container registry 
1. _Quite a bit more..._

Root system that needs to be selected is a cluster and/or container management platform. 

Couple of months ago, [DC/OS][2] project [was announced][1] as compilation of Mesos, Marathon and
Mesosphere Datacenter Operating System. Among partners are such companies as Microsoft, Cisco, 
Confluent, HP, Citrix, Autodesk etc. Today Mesos can run Kubernetes and YARN, but vice versa is
not something typical. Mesos also integrates well with IaaS stacks, such as OpenStack, CloudStack etc.
Also, we already have experience with Mesos+Marathon in Paralect. This naturally leads to selection
of DC/OS or Mesos as foundation of Plato.

Another mentioned contender is Kubernetes. This project originates from Google, based on in-house cluster 
manager Borg. Kubernetes is considered production ready, and is available as cloud service: Google Container 
Engine. Also, Red Hat's OpenShift is based on this technology. Kubernates was designed from "container" 
point of view from the beginning, while Mesos run through some evolution in order to support containers. 

Next option is a construction of container management solution from lower-level components, like etcd,
systemd, fleet. The most complex way. 

In any way, things can change, and when they will change we should not cry, but instead gradually 
adopt _next generation solution™_.

## Consumer-oriented products

Besides common infrastructure services and frameworks, Plato will 
consists of at least the following consumer-oriented products:

1. Paralect internal and external sites, apps, mobile apps, services and tools
2. Robomongo site and services

## Shared cluster for PaaS services

Some services are well consumable by any application, even when application
is not part of Plato. For example, scheduler that communicates over HTTP or 
image processing services, or grid computation, or WebDriver tests etc. 

It means that Plato should be always running, always available and always accessable
by Paralect teammates and automated processes.

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
