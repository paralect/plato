*Notes about CoreOS from "Mastering CoreOS" book by Sreenivas Makam and some other resources. To be better summarized.*

The first container-optimized OS. First alpha release was in July 2013. Based on principles 
from the Google Chrome OS. No package management is provided by the OS. Libraries and packages 
are part of the application developed using Containers. Container runtime, SSH, and kernel are 
the primary components. Every process is managed by systemd. The management of CoreOS machines 
is done at the cluster level rather than at an individual machine level.

The kernel auto-update feature protects the kernel from security vulnerabilities. The CoreOS 
memory footprint is very small.

Core components: Kernel, systemd, etcd, fleet, flannel, and rkt

### Etcd discovery

The members in the cluster discover themselves using either a static approach or dynamic
approach. In the static approach, we need to mention the IP addresses of all the neighbors
statically in every node of the cluster. In the dynamic approach, we use the discovery
token approach where we get a distributed token from a central etcd server and use this in
all members of the cluster so that the members can discover each other.

Get a distributed token as follows:
curl https://discovery.etcd.io/new?size=<size>

The discovery token feature is hosted by CoreOS and is implemented as an etcd cluster as
well.

#### Cluster size

It is preferable to have an odd-sized etcd cluster as it gives a better failure tolerance.
With a cluster size of two, we cannot determine majority.

#### Etcd cluster details

List all member nodes in the cluster

    etcdctl member list

Cluster health:

    etcdctl cluster-health 
    
#### Fleet

Fleet is a cluster manager/scheduler that controls service creation at the cluster level. Like
systemd being the init system for a node, Fleet serves as the init system for a cluster. Fleet
uses etcd for internode communication.

Systemd provides HA at the node level; Fleet provides HA at the cluster level.

Fleet is typically used for the orchestration of critical system services using
systemd while Kubernetes is used for application container orchestration.

#### Flannel

Flannel uses an Overlay network to allow Containers across different hosts to talk to each
other. Flannel is not part of the base CoreOS image. This is done to keep the CoreOS
image size minimal. When Flannel is started, the flannel container image is retrieved from
the Container image repository. The Docker daemon is typically started after the Flannel
service so that containers can get the IP address assigned by Flannel. This represents a
chicken-and-egg problem as Docker is necessary to download the Flannel image. The
CoreOS team has solved this problem by running a master Docker service whose only
purpose is to download the Flannel container.

The following are some Flannel internals:

0. Flannel runs without a central server and uses etcd for communication between the
nodes
1. As part of starting Flannel, we need to supply a configuration file that contains the IP
subnet to be used for the cluster as well as the backend protocol method.
2. Each node in the cluster requests an IP address range for containers created in that
host and registers this IP range with etcd.
3. As every node in the cluster knows the IP address range allocated for every other
node, it knows how to reach containers created on any node in the cluster.
4. When containers are created, containers get an IP address in the range allocated to the
node.
5. When Containers need to talk across hosts, Flannel does the encapsulation based on
the backend encapsulation protocol chosen. Flannel, in the destination node, deencapsulates
the packet and hands it over to the Container.
6. By not using port-based mapping to talk across containers, Flannel simplifies
Container-to-Container communication.


#### Rocket (rkt)

Rkt is the Container runtime developed by CoreOS. Rkt does not have a daemon and is
managed by systemd. Rkt uses the Application Container image (ACI) image format,
which is according to the APPC specification (https://github.com/appc/spec). Rkt’s
execution is split into three stages. This approach was taken so that some of the stages can
be replaced by a different implementation if needed. Following are details on the three
stages of Rkt execution.

rkt's version 1.0 release marks the command line user interface and on-disk data structures 
as stable and reliable for external development.

rkt can also 
[run Docker images](https://github.com/coreos/rkt/blob/master/Documentation/running-docker-images.md).

## The CoreOS cluster architecture

Nodes in the CoreOS cluster are used to run critical CoreOS services such as etcd, fleet,
Docker, systemd, flannel, and journald as well as application containers. It is important to
avoid using the same host to run critical services as well as application containers so that
there is no resource contention for critical services. This kind of scheduling can be
achieved using the Fleet metadata to separate the core machines and worker machines.

We can have a three or five-node master cluster to run critical CoreOS services and then
have a dynamic worker cluster to run application Containers. The master cluster will run
etcd, fleet, and other critical services. In worker nodes, etcd will be set up to proxy to
master nodes so that worker nodes can use master nodes for etcd communication. Fleet, in
worker nodes, will also be set up to use etcd in master nodes.

#### APPC versus OCI

[APPC](https://github.com/appc/spec) and [OCI](https://github.com/opencontainers/specs)
define Container standards.

The APPC specification is primarily driven by CoreOS along with a few other community
members. The APPC specification defines the following:

1. Image format: Packaging and signing
2. Runtime: How to execute the Container
3. Naming and Sharing: Automatic discovery

APPC is implemented by Rkt, Kurma, Jetpack, and others.

OCI (https://www.opencontainers.org/) is an open container initiative project started in
April 2015 and has members from all major companies including Docker and CoreOS.
Runc is an implementation of OCI.

#### Cloud-config

Cloud-config is a declarative configuration file format that is used by many Linux
distributions to describe the initial server configuration. The cloud-init program takes care
of parsing cloud-config during server initialization and configures the server
appropriately. The cloud-config file provides you with a default configuration for the
CoreOS node.

[cloud-init](https://cloudinit.readthedocs.io/en/latest/) — "set of python scripts and utilities 
to make your cloud images be all they can be!"

The `coreos-cloudinit` program takes care of the default configuration of the CoreOS
node during bootup using the cloud-config file. The cloud-config file describes the
configuration in the YAML format. CoreOS cloud-config follows
the cloud-config specification with some [CoreOS-specific](https://coreos.com/os/docs/latest/cloud-config.html) 
options. 

Cloud-config has such fields as `ssh_authorized_keys`, `manage_etc_hosts`, `users`

#### Executing cloud-config

There are two cloud-config files that are run as part of the CoreOS bootup:

1. System cloud-config
2. User cloud-config

System cloud-config is given by the provider (such as Vagrant or AWS) and is embedded
as part of the CoreOS provider image. Different providers such as Vagrant, AWS, and
GCE have their cloud-config present in `/usr/share/oem/cloud-config.yaml`. This
cloud-config is responsible for setting up the provider-specific configurations, such as
networking, SSH keys, mount options, and so on. The coreos-cloudinit program first
executes system cloud-config and then user cloud-config.

Depending on the provider, user cloud-config can be supplied using either config-drive
or an internal user data service. Config-drive is a universal way to provide cloud-config
by mounting a read-only partition that contains cloud-config to the host machine.
Rackspace uses config-drive to get user cloud-config, and AWS uses its internal user
data service to fetch the user data and doesn’t rely on config-drive. In the Vagrant
scenario, Vagrantfile takes care of copying the cloud-config to the CoreOS VM.

#### Basic debugging

The following are some basic debugging tools and approaches to debug issues in the
CoreOS cluster.

**journalctl**

Systemd-Journal takes care of logging all the kernel and systemd services. Journal log
files from all the services are stored in a centralized location in `/var/log/journal`. The
logs are stored in the binary format, and this keeps it easy to manipulate to different
formats.

    journalctl    # lists the combined journal log from all the sources
    journalctl –u etcd2.service                  # This lists the logs from etcd2.service
    journalctl –u etcd2.service –no-pager        # This lists the logs with no pagination, 
                                                 #   which is useful for search
    journalctl –p err –n 100                     # This lists all 100 errors by filtering the logs
    journalctl -u etcd2.service —since today:    # This lists today’s logs of etcd2.service
    journalctl -u etcd2.service -o json-pretty   # This lists the logs of etcd2.service in 
                                                 #   JSON-formatted output

**systemctl**

The systemctl utility can be used for basic monitoring and troubleshooting of the
systemd units.

systemctl restart docker.service 


References:

1. [Mastering CoreOS](https://www.amazon.com/Mastering-CoreOS-Sreenivas-Makam-ebook/dp/B01AI0NKRQ#customerReviews) by Sreenivas Makam, February 2016
2. [rkt is a container engine](https://github.com/coreos/rkt)
