<!--- master only -->
> ![warning](../images/warning.png) This document applies to the HEAD of the calico-containers source tree.
>
> View the calico-containers documentation for the latest release [here](https://github.com/projectcalico/calico-containers/blob/v0.21.0/README.md).
<!--- else
> You are viewing the calico-containers documentation for release **release**.
<!--- end of master only -->

# Installing Calico-Mesos Prerequisites
This guide details a minimal install of the external services required by a Calico-Mesos Agent.
- **A Note on Clustered Services**: In general, a production-ready
mesos cluster should run each of these services across multiple
highly available hosts with a quorum. However, for demo and
development purposes, **this guide will cover launching only one
of each service on a single centos7 host.** Consult the appropriate
documentation for each service:
    - [etcd clustering](https://coreos.com/etcd/docs/latest/clustering.html)
    - [zookeeper multi-server setup](https://zookeeper.apache.org/doc/r3.3.2/zookeeperAdmin.html#sc_zkMulitServerSetup)
    - [mesos high-availability mode](http://mesos.apache.org/documentation/latest/high-availability/)

    For more information on how to set up a more production-ready
    calico-mesos cluster in such a manner, [contact us on slack][slack].

- **A Note on Calico's Customizations in Mesos**: It is important
to note that no customizations are necessary for any of the
aforementioned services to be compatible with a Calico-Mesos Agent.
**Adding calico to a mesos cluster only requires modifications to each Agent.**


## Prerequisite: Add the Mesos Official Repository
Mesos-master, zookeeper, and marathon are installed from the official Mesos repository. Add the official repository:

    rpm -Uvh http://repos.mesosphere.com/el/7/noarch/RPMS/mesosphere-el-repo-7-1.noarch.rpm

## ZooKeeper
Install Mesos' datastore - ZooKeeper:

```
yum install -y mesosphere-zookeeper
systemctl start zookeeper
```

ZooKeeper uses tcp over port 2181. If you're using a firewall, open this port:

```
sudo firewall-cmd --zone=public --add-port=2181/tcp --permanent
sudo systemctl restart firewalld
```

## Marathon
Supported Versions: `v1.0.0` (recommended), `v0.14.0` - `v0.14.2`

Once mesos-master is running, we can launch Marathon:

```
sudo yum install -y marathon
systemctl start marathon
```

Marathon listens for tcp connections on port 8080. If you're using a firewall,
open this port:

```
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
sudo systemctl restart firewalld
```

## etcd
Calico uses etcd as its data store and communication mechanism among Calico components. Install it from Centos' repositories with the following command, replacing `$IP` with the cluster-accessible IP you would like etcd to bind to:

```
yum install -y etcd
echo ETCD_LISTEN_CLIENT_URLS=\"http://0.0.0.0:2379\" >> /etc/etcd/etcd.conf
echo ETCD_ADVERTISE_CLIENT_URLS=\"http://$IP:2379\" >> /etc/etcd/etcd.conf
systemctl start etcd.service
```

Etcd listens for tcp connections on port 2379. If you're using a firewall,
open this port:

```
sudo firewall-cmd --zone=public --add-port=2379/tcp --permanent
sudo systemctl restart firewalld
```

## Next steps
Check out one of our guides on using Calico-Mesos with the
[Unified Containerizer](UsageGuideUnifiedContainerizer.md) or
the [Docker Containerizer](UsageGuideDockerContainerizer.md)
to see how to launch tasks networked with Calico.

[slack]: https://calicousers.slack.com
[![Analytics](https://calico-ga-beacon.appspot.com/UA-52125893-3/calico-containers/docs/mesos/MesosClusterPreparation.md?pixel)](https://github.com/igrigorik/ga-beacon)

