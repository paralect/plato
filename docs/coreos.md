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

    systemctl status docker.service
    sudo systemctl restart docker.service        # Restart service
    
When a service file is changed or environment variables are changed, we need to execute
the following command to reload configuration before restarting the service for the
changes to take effect:

    sudo systemctl daemon-reload
    
The following command is useful to see the units that have failed:

    systemctl —failed

**Cloud-config**

Cloud-config can be validated. 

In case there are runtime errors, we can check it with:

    journalctl -b _EXE=/usr/bin/coreoscloudinit
    
#### Important files and directories

1. The machine ID for the particular CoreOS node - /etc/machine-id
2. The public and private IP address (COREOS_PUBLIC_IPV4 and COREOS_PRIVATE_IPV4) - /etc/environment
3. cloud-config.yaml associated with providers such as Vagrant, AWS, and GCE-
/usr/share/oem/cloud-config.yaml. (CoreOS first executes this cloud-config and then executes the
user-provided cloud-config.)
4. Release channel and update strategy - /etc/coreos/update.conf
5. The systemd-journald logs - /var/log/journal

#### Common mistakes and possible solutions

1. For CoreOS on the cloud provider, there is a need to open up ports 2379 and 2380 on
the VM. 2379 is used for etcd client-to-server communication, and 2380 is used for
etcd server-to-server communication.
2. A discovery token needs to be generated every time for each cluster and cannot be
shared. When a stale discovery token is shared, members will not be able to join the
etcd cluster.
3. Running multiple CoreOS clusters with Vagrant simultaneously can cause issues
because of overlapping IP ranges. Care should be taken so that common parameters
such as the IP address are not shared across clusters.
4. Cloud-config YAML files need to be properly indented. It is better to use the cloudconfig
validator to check for issues.
5. When using discovery token, CoreOS node needs to have Internet access to access
the token service.
6. When creating a discovery token, you need to use the size based on the count of
members and all members need to be part of the bootstrap. If all members are not
present, the cluster will not be formed. Members can be added or removed later.

#### CoreOS release cycle

Alpha, Beta, and Stable are release channels within CoreOS. CoreOS releases progress
through each channel in this order: Alpha->Beta->Stable.

The major version number (for example, 1164 in 1164.3.0) is the number of days from July
13, 2013, which was the CoreOS epoch.

    cat /etc/os-release      # Check the CoreOS version in the node

#### Partition table

    sudo cgpt show /dev/xvda   # Shows partition table in one of the CoreOS cluster nodes
    df –k                      # GNU tools are available (free space)
    
#### CoreOS automatic update

CoreOS relies on the automatic update mechanism to keep the OS up to date.
The following are some aspects of the CoreOS update:

1. The CoreOS update mechanism is based on Google’s open source Omaha protocol
(https://code.google.com/p/omaha/) that is used in the Chrome browser.
2. Either CoreOS public servers or private servers can be used as an image repository.
3. The dual partition scheme is used where an update is done to the secondary partition
while the primary partition is not touched. On reboot, there is a binary swap from the
primary to the secondary partition. This keeps the update scheme robust. If there are
issues with the new image, CoreOS automatically rolls back to the working image in
the other partition.
4. Images are signed and verified on each update.

#### Update and reboot services

There are two critical services controlling update and reboot in CoreOS. They are updateengine.
service and locksmithd.service

`update-engine.service` takes care of periodically checking for updates from the
appropriate release channel specified. A default check for update is done 10 minutes after
reboot or at one-hour intervals.

    systemctl status update-engine.service          # Shows the status of service

`locksmithd.service` takes care of rebooting the CoreOS node using the selected reboot
strategy. Locksmithd.service runs as a daemon.

The following are the four configurable strategies for the CoreOS node reboot after a new
image update.

1. The `etcd-lock` scheme. In this scheme, the reboot is done after first taking a lock from etcd. In a multinode
cluster, this works out really well as it prevents all the nodes from rebooting at the same
time and maintains cluster integrity. We can control the number of nodes that can reboot
together using the lock count mechanism.
2. `reboot`. In this scheme, the node is rebooted immediately without taking a lock from the cluster.
This is useful in scenarios where the upgrade is manually controlled by the administrator
3. `best-effort`. In this scheme, it is first checked whether etcd is running. If etcd is running, the etcd
lock is acquired and then the rebooting is done. Otherwise, reboot is done immediately.
This is a variation of the etcd-lock scheme mentioned before.
4. `off`. This causes locksmithd to exit and do nothing. This option should not be chosen unless the
administrator wants to control the upgrades with great precision.

Locksmith groups were introduced in locksmithd version 0.3.1. With groups, we can
group a set of CoreOS nodes and locking will be applicable to this group.

`locksmithctl` is a frontend CLI to control Locksmith. Using this, we can get the status of
locksmith service, lock and unlock groups, set the lock max count, and so on.

CoreOS update options can be set using either cloud-config or by changing
configuration files manually. Using cloud-config, update options are configured as part
of the node configuration after reboot. With the manual approach, we need to start the
appropriate update services for changes to take effect. The manual approach is used
mainly to debug.




References:

1. [Mastering CoreOS](https://www.amazon.com/Mastering-CoreOS-Sreenivas-Makam-ebook/dp/B01AI0NKRQ#customerReviews) by Sreenivas Makam, February 2016
2. [rkt is a container engine](https://github.com/coreos/rkt)
