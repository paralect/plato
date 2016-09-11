# 🐒 Plato

_This is a draft of the plan for Plato. Several technologies proposed and more to come
soon._

Plato is a collection of _integrated_ applications, services, tools and guidelines written and managed 
as a _single unit_. Plato provides both foundation for your project and reference implementation
of several applications used by Paralect. 

Plato is commited to be modern and up to date. Bleeding edge technologies are also welcome,
as long as reliability and stability are not sacrified. Because landscape of tools, frameworks and
technologies constantly changes and evolves, someone should track and prepare the way for the changes.
This is the role that Plato will try to play. When it is time to move forward, you will receive 
upgrade guidelines and all required documentation.

📢 Plato serves as a communication channel and experimentation playground for all Paralect teammates.
Any proposal, suggestion, fix or implementation are welcome!

## Overview

1. Monorepo. All platforms and tech stacks inside one repo. 
2. Trunc based development. Branches only for releases (almost).
3. Git LFS for large files (like PSD, MP4, etc)
4. Third party software can be both open source or proprietary
5. Third party software can be both hosted or available in the cloud
6. Paralect specific apps and tools are also part of Plato

# Technology Proposal

_This is a preliminary proposal that is changing every day. Share your 
opinion or vote for the particualar technology._ 

In two sentences: _"Google architecture for infrastructure. Facebook architecture for applications."_

🔹**Core Technologies**

1. **[DigitalOcean](https://www.digitalocean.com/)** as IaaS Provider with deep integration via [API](https://developers.digitalocean.com/documentation/). In the future consider [AWS](https://aws.amazon.com/), [Azure](https://azure.microsoft.com/en-us), [Google Cloud Platform](https://cloud.google.com), [OpenStack](https://www.openstack.org/) in yet unknown order. 
1. **[Kubernetes](http://kubernetes.io/)** for Cluster and Container Management
2. **[GlusterFS](https://www.gluster.org/) or [Torus](https://github.com/coreos/torus)** as Network Filesystem ([SDS](https://en.wikipedia.org/wiki/Software-defined_storage)) 
3. **[CoreOS](https://coreos.com/)** as Cluster Operating System
4. **[systemd](https://freedesktop.org/wiki/Software/systemd)** as Linux Init System
5. **[Docker](https://www.docker.com/)** as Container Runtime
6. **[Prometheus](https://prometheus.io)** as Container Cluster Monitoring: instrumentation, collection, querying, and alerting.
7. **[Alpine](http://www.alpinelinux.org/)** as Linux Distribution for Docker Images (if possible)

🔹**Infrastructure Development**

1. **[Go](https://golang.org/)** as preferable language, if makes sense. Any choice is permitted. 

🔹**Application Development**

1. **Single JavaScript Specification** across all engines (V8, Nashorn, SpiderMonkey, Chakra, Nitro) and use cases (Browser, Desktop, Mobile, Server)
2. **ES6/ES7** as Language Dialect (TODO: specify more strictly in a form of Babel config flags)
3. **[Babel](https://babeljs.io/)** as Transpiler
4. **[Flow](https://flowtype.org/)** as Typechecker
5. **[React](https://facebook.github.io/react/)** and **[React Native](https://facebook.github.io/react-native/)** for UI 
6. **Linux** and **OS X** as supported development platforms from day one. Windows in a few months.

🐥 Nothing is finally selected! This is a proposal from which to start investigations. Share 
your ideas!


# Principles and goals

1. Container-centric infrastructure. User creates and manages containers and never physical or virtual machines. In terms of DigitalOcean, for example, it means that Plato creates and deletes droplets via [DigitalOcean API](https://developers.digitalocean.com/documentation). 
2. Container-centric development. Developer consumes databases and tools wrapped in containers. Instead of `apt-get install mongodb` developer should do `docker pull mongodb`. 
3. Managing storage is a distinct problem from managing compute. Should be a clear separation of how storage is provided from how it is consumed. On commodity hardware this implies usage of Network Filesystems, like GlusterFS, Ceph, Torus etc. 
4. Plato cluster is controlled via REST API by `plato` command and web UI. 
5. Single command to deploy and provision fully functional Plato cluster. 
6. One process per container. 

# Things to watch out 

Here we are keeping our eyes on different technologies that are potential candidates for adoption. 
Things evolve rapidly and the following list also should be up to date. 

Regardless of our today's choice, we should constantly track the most notable technologies and services. 

#### 🌏 **IaaS Providers**

Ideally, we should support all major providers :) Today we need at least one.

1. **[DigitalOcean](https://www.digitalocean.com/)** is one of the most simple and affordable providers with clean price and [API](https://developers.digitalocean.com/documentation/). The only downside is a limited functionality, comparing with more mature IaaS platforms. Slowly, but they are progressing. At July 13, 2016 they released [Block Storage](https://www.digitalocean.com/company/blog/block-storage-more-space-to-scale/) that will try to compete with [EBS](https://aws.amazon.com/ebs/), [GPS](https://cloud.google.com/compute/docs/disks/) and similar. Not sure that DigitalOcean has everything that we need, but it worth to at least try it.
2. [AWS](https://aws.amazon.com/), [Azure](https://azure.microsoft.com/en-us), [Google Cloud Platform](https://cloud.google.com) are the most mature platforms. Each platform is considered as a safe bet.   
3. **[OpenStack](https://www.openstack.org/)** is a platform for deploying and managing private or public IaaS clusters. Usually installed on bare-metal machines. 

#### 🚉  **Network Filesystem**

Major platforms provide some form of [Software-defined Storage](https://en.wikipedia.org/wiki/Software-defined_storage). Amazon Elastic Block Storage, Google Persistent Disk and Digital Ocean Block Storage. But some still run Network Filesystem on top of this storages: [NASA Jet Propulsion Laboratory Case Study](https://aws.amazon.com/solutions/case-studies/nasa-jpl-curiosity/).  

We need Network Filesystem to abstract storage from computing nodes. More specifically, we need to provide persistent storage for containers. 

1. **GlusterFS**. Production ready. Used by OpenShift platform.
2. **CephFS**. Production ready starting from [April 21, 2016](http://thenewstack.io/converging-storage-cephfs-now-production-ready).
3. **Torus**. Created and supported by the CoreOS team. Claims to be a "[cloud-native and modern distributed storage](https://coreos.com/blog/torus-distributed-storage-by-coreos.html)". Not ready for production, early days. 
Still, it make sense to give Torus a try.
4. **Flocker.**

#### 🚀 **Cluster and Container Management**

1. **Kubernetes.** Originates from Google, 
based on in-house cluster manager [Borg](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/43438.pdf). Kubernetes is considered production ready, and is available as cloud service: Google Container 
Engine. Also, Red Hat's OpenShift is based on this technology. Kubernates was designed from "container" 
point of view from the beginning.
2. **Mesos.** Known and battle proven on large scale clusters in Twitter, Facebook and LinkedIn.
3. **DC/OS.** At April 19, 2016 [DC/OS][2] project [was announced][1] as compilation of Mesos, Marathon and
Mesosphere Datacenter Operating System. Among partners are such companies as Microsoft, Cisco, 
Confluent, HP, Citrix, Autodesk etc.
4. **Docker Swarm**. Released at June 20, 2016 with [Docker 1.12](https://blog.docker.com/2016/06/docker-1-12-built-in-orchestration). "Native" clustering for Docker. 
5. **Fleet**. 

#### 💻 **Container Operating System**

There’s been an explosion of new container-centric operating systems. We need at least one in the 
beginning.

1. **CoreOS**. The first container-optimized OS. First alpha release was in July 2013. Based on principles from the Google Chrome OS. No package management is provided by the OS. Libraries and packages are part of the application developed using Containers. Container runtime, SSH, and kernel are the primary components. Every process is managed by systemd. The management of CoreOS machines is done at the cluster level rather than at an individual machine level.
2. **Ubuntu Snappy**.
3. **Red Hat Project Atomic**.
4. **RancherOS**.
5. **VMware Photon**.
6. **Microsoft Nano Server**.

#### 🔲 **Containers Runtime**

1. **Docker.**
2. **Rocket (rkt).** Rkt is the Container runtime developed by CoreOS. Rkt does not have a daemon and is
managed by `systemd`. Rkt uses the Application Container image (ACI) image format,
which is according to the [Appc specification](https://github.com/appc/spec). Rkt’s
execution is split into three stages. This approach was taken so that some of the stages can
be replaced by a different implementation if needed. Rkt can also 
[run Docker images](https://github.com/coreos/rkt/blob/master/Documentation/running-docker-images.md).
1. **Appc.** Not runtime but "well-specified and community developed specification for application containers". Started
by CoreOS, but initially [gained support](https://www.infoq.com/news/2015/05/appc-spec-gains-support) from Google, Apcera, Red Hat and VMware.

#### 📡 **Networking**

Virtual networks that are portable across data centers and public clouds.

1. **[Flannel](https://coreos.com/flannel/docs/latest/).** Virtual network maintained by the CoreOS project. 
2. **[Calico](https://www.projectcalico.org).** 
3. **[Weave](https://www.weave.works/).** Simple, resilient multi-host Docker networking.

#### 🚥 **Service Discovery and Configuration**

Distributed, consistent key-value stores for shared configuration and service discovery.

1. **ZooKeeper.**
2. **etcd.**
3. **Consul.**

#### 🔀 **Load Balancers and Proxies**

1. **Nginx.**
2. **HAProxy.**

#### 📈 **Monitoring**

1. **[Prometheus](https://prometheus.io).** Monitoring system and time series database. Together with
Kubernetes, Prometheus is a member of [Cloud Native Computing Foundation](https://cncf.io/projects).
Actively [promoted](https://coreos.com/blog/coreos-and-prometheus-improve-cluster-monitoring.html) by CoreOS.
2. **[cAdvisor](https://github.com/google/cadvisor).** Analyzes resource usage and performance characteristics of running containers. Google project. Has native support for Docker containers.
3. **[Sysdig](http://www.sysdig.org/).** Sysdig is open source, system-level exploration: capture system state and activity from a running Linux instance, then save, filter and analyze. 
4. **[NewRelic](https://newrelic.com/).** Commercial, but good :)

#### 📦 **Hardware Virtualization and Hypervisors**

For now, we mostly interested in Type-2 hypervisors. They allow to run local cluster of unmodified operating systems on top of conventional operating systems. This is required mostly for low-level work on infrastructure level.  

1. **VirtualBox.** Open source Type-2 hypervisor.
2. **Vagrant.** Not directly related to virtualization, but provides convenient tools to manage local virtual machines.  
3. **Hyper-V** and **VMware ESXi**. Commercial Type-1 hypervisors.
4. **VMWare Workstation/Fussion.** Commercial Type-2 hypervisor. 

#### 🌀 **UNIX init systems**

Init system is important, because it is a built-in and "OS native" functionality to manage lifetime of processeses
and, with modern init systems, even containers. Besides this, init system defines behaviour of operating system 
throughout the lifetime.

1. **[systemd](https://freedesktop.org/wiki/Software/systemd).** Released at 2010 by RedHat developer [Lennart Poettering](https://en.wikipedia.org/wiki/Lennart_Poettering) and today is adopted by most major Linux distributions,
including Ubuntu, Debian, OpenSUSE, Oracle Linux, CoreOS etc. Scope of this project is fascinating. Motivation
for new init system is explained by Poettering in his [6 years old article](http://0pointer.de/blog/projects/systemd.html).
2. **[Upstart](http://upstart.ubuntu.com).** Released at 2006 by Canonical. At some point it was adopted even by RedHat.
But today seems loosing positions to systemd.
3. **[System V init](https://en.wikipedia.org/wiki/Init#SYSV).** Traditional UNIX System V init system that everybody
tries to replace :)

## Plan ideas

Three ways:

1. **Simple.** Start from Kubernetes cluster managed by [Google Container Engine](https://cloud.google.com/container-engine/). Use services that build and distribute Docker images (like [Google Container Builder](https://cloud.google.com/container-builder), [Google Container Registry](https://cloud.google.com/container-registry), [Codeship](codeship.com), [Quay](https://quay.io/) etc.).
2. **Complex.** Integrate with IaaS (like DigitalOcean) and deploy Kubernetes cluster manually. Deploy our own Docker Image Builder and Registry. Right now Kubernetes do not support just released DigitalOcean [Block Storage](https://www.digitalocean.com/products/storage/) as Persistent Volume which is required for statefull containers. This should be implemented or some workarounds like [Flocker](https://clusterhq.com/flocker) should be used. Or we can use [Google Compute Engine](https://cloud.google.com/compute) (this is not Container Engine), [AWS EC2](https://aws.amazon.com/ec2/) or any other supported by Kubernetes IaaS.
4. **Normal.** Mix of previous two ways. 

Complex way:

```
   ---------------
   |  Plato CLI  |
   ---------------
          |
          |
---------------------------------------
|  Plato REST API  |   Plato Web UI   |
---------------------------------------
|           Plato Cluster             |
---------------------------------------
```

1. Deploy Kubernetes cluster with the help of DigitalOcean API. Simplify it to the point when Kubernetes can be deployed with single shell command. Deployment is complete when Plato API service is running and publicly accessible via HTTP. Further communication with the cluster should be using Plato API and _not_ Kubernetes API. 
2. Create service that automatically builds Docker images, stores them and plays a role of Docker Registry. This service can be controlled via Plato API: enable/disable/configure.
3. Create simple web dashboard which allows to control cluster and view some cluster information. All needed functionality should be provided by Plato API. This dashboard should be enabled by default with every Plato installation. 

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
1. Rolling update 
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

Another mentioned contender is [Kubernetes](http://kubernetes.io). This project originates from Google, 
based on in-house cluster manager [Borg](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/43438.pdf). Kubernetes is considered production ready, and is available as cloud service: Google Container 
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
