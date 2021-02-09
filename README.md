# Create a Local Consul Datacenter

## Prerequisites
- [X] [Install VirtualBox](https://www.virtualbox.org/)
- [X] [Install Vagrant](https://www.vagrantup.com/downloads)

## What's included

- Vagrantfile based on the content of Consul's demo [Vagrantfile](https://github.com/hashicorp/consul/blob/master/demo/vagrant-cluster/Vagrantfile)

## Instructions

### Clone this Repository

```shell
git clone git@github.com:dlavric/consul-datacenter.git
```

### Access the Repository

```shell
cd consul-datacenter
```

### Start Vagrant

```shell
vagrant up
```

### Configure the datacenter

- Once the machine is up, ssh into node 1:

```shell
vagrant ssh n1
```

- Start the Consul agent:

```shell
 consul agent \
  -server \
  -bootstrap-expect=1 \
  -node=agent-one \
  -bind=172.20.20.10 \
  -data-dir=/tmp/consul \
  -config-dir=/etc/consul.d
```

- Open a new terminal window, ssh into node 2:

```shell
vagrant ssh n2
```

- Start the 2nd Consul Agent:

```shell
 consul agent \
  -node=agent-two \
  -bind=172.20.20.11 \
  -enable-script-checks=true \
  -data-dir=/tmp/consul \
  -config-dir=/etc/consul.d
```

- Open a new terminal, ssh into node 2:

```shell
vagrant ssh n2
```

- Access the members of Consul:
```shell
consul members
```

    Output:
    ```
    vagrant@n2:~$ consul members
    Node       Address            Status  Type    Build  Protocol  DC   Segment
    agent-two  172.20.20.11:8301  alive   client  1.9.3  2         dc1  <default>
    ```

- Open a new terminal, ssh into node 1:

```shell
 vagrant ssh n1
```

- Access the members of Consul's node 1:

```shell
consul members
```

    Output:
    ```
    vagrant@n1:~$ consul members
    Node       Address            Status  Type    Build  Protocol  DC   Segment
    agent-one  172.20.20.10:8301  alive   server  1.9.3  2         dc1  <all>
    ```

- Join the Consul Agents from the node 1:

```shell
 consul join 172.20.20.11
```
    
    Output:
    ```
    Successfully joined cluster by contacting 1 nodes.
    ```

Now we will see both members in the node 1, by checking the consul members again:

```shell
consul members
```

    Output:
    ```
    Node       Address            Status  Type    Build  Protocol  DC   Segment
    agent-one  172.20.20.10:8301  alive   server  1.9.3  2         dc1  <all>
    agent-two  172.20.20.11:8301  alive   client  1.9.3  2         dc1  <default>
    ```

- Query a Consul agent's node using DNS interface:

```shell
dig @127.0.0.1 -p 8600 agent-two.node.consul
```

- Stop the Consul agent with 'CTRL+C' from the terminal where Consul node 1 and 2 are running


- Destroy the machine when you are finished:

```shell
vagrant destroy
```







