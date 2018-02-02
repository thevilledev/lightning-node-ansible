# lightning-node-ansible

This repository contains configuration for creating automated Bitcoin Core + Lightning Network
installations by utilizing Ansible. A blog post by @dougvk outlines the manual setup very nicely:
https://medium.com/@dougvk/run-your-own-mainnet-lightning-node-2d2eab628a8b

Each node runs the official Bitcoin Core binaries as described in the blog post, except I have
built the container by using multi-stage builds. This minimizes the amount of data Docker has to
pull upon container startup. This is about 30 MB. Container configuration can be found from
vtorhonen/lightning-node and from Dockerhub: https://hub.docker.com/r/vtorhonen/lightning-node/

# Prequisites

Deploy a fleet of Debian hosts. Make sure you have plenty of storage space available under `/data` directory on each host. This playbook expects that.

Next define your hosts in `inventories/lnd-nodes.yml`. See [inventories/example.yml] for
reference.

As a final step install Ansible to your control machine. This playbook has been tested by using Ansible 2.4.3.

# Installing bitcoind nodes

First run the playbook `deploy-bitcoind.yml`, which installs Docker and runs the Bitcoin Core
container.

```
$ ansible-playbook -i inventories/lnd-nodes.yml deploy-bitcoind.yml
```

You now have a Bitcoin Core node running, which exposes itself to the network via `tcp/8333`
and `tcp/9735` ports. Node start synchronizing the blockchain. Use the following command to see
it's progress.

```
$ ansible all -b -i inventories/lnd-nodes.yml -a "docker logs --tail=1 bitcoind"
foohost | SUCCESS | rc=0 >>
2018-02-02 20:15:07 UpdateTip: new best=0000000000000007b4511e0e0fe84f191bf6451e2e31e33da5445ec298e40c6c height=263627 version=0x00000002 log2_work=72.758038 tx=25379178 date='2013-10-14 16:04:00' progress=0.086696 cache=570.9MiB(4120711txo)
```

# Installing lightning network nodes

TBA
