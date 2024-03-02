# Setting Up Network namespaces and Bridge Connectivity

## Introduction

In this tutorial, we'll walk through the process of creating two network namespaces, connecting them with a bridge, and testing connectivity by pinging Google's public IP. This setup can be useful for isolating network environments and testing network configurations.


## Step 1: Create Network Namespaces

```bash
sudo ip netns add red
sudo ip netns add green

sudo ip netns show
```

## Step 2: Create a Bridge and Assign an IP

```bash
sudo ip link add br0 type bridge

sudo ip addr add 192.168.0.1/24 dev br0
sudo ip link set br0 up

ping 192.168.0.1 -c 2
```

## Step 3: Connect Namespaces and Bridge

```bash
sudo ip link add veth-red-ns type veth peer name veth-red-br
sudo ip link add veth-green-ns type veth peer name veth-green-br

sudo ip link set veth-red-ns netns red
sudo ip link set veth-green-ns netns green

sudo ip link set veth-red-br master br0
sudo ip link set veth-green-br master br0

sudo ip link set veth-red-br up
sudo ip link set veth-green-br up

```

## Step 4: Setup Red Namespace

```bash
sudo ip netns exec red bash

ip link set veth-red-ns up
ip addr add 192.168.0.2/24 dev veth-red-ns

ping 192.168.0.1 -c 2
ip route add default via 192.168.0.1

# My root ns ip 10.42.1.158
ping -c 2 10.42.1.158

exit
```

## Step 5: Setup Green Namespace

```bash
sudo ip netns exec green bash

ip link set veth-green-ns up
ip addr add 192.168.0.3/24 dev veth-green-ns

ping 192.168.0.1 -c 2
ip route add default via 192.168.0.1

# My root ns ip 10.42.1.158
ping -c 2 10.42.1.158

exit
```

## Step 6: Enable SNAT Rule for Masquerading

```bash
sudo iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -j MASQUERADE
```

## Step 7: Test Connectivity to Google's Public IP

```bash
# Test from Red Namespace
sudo ip netns exec red ping 8.8.8.8 -c 2

# Test from Green Namespace
sudo ip netns exec green ping 8.8.8.8 -c 2
```

## Conclusion
Congratulations! We've successfully set up network namespaces, connected them with a bridge, and tested connectivity to Google's public IP.
