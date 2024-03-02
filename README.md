# Make two network namespaces, connect them with a bridge and check connectivity, ping Google's public IP from those network namespaces

## Lets go..

### Make two network namespaces

>
> `sudo ip netns add red`
>
> `sudo ip netns add green`
>
> `sudo ip netns show`
>

----------------------

### Make a bridge, assign ip

>
> `sudo ip link add br0 type bridge`
>
> `sudo ip addr add 192.168.0.1 dev br0`
>
> `sudo ip link set br0 up`
>
> `ping 192.168.0.1 -c 2`
>

------------------------------
