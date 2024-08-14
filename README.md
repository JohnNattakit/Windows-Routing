```
Currently, I have two network interfaces that

1) Wireless LAN adapter Wi-Fi 3: (Access Internet)

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::c409:4503:5556:426d%16
   IPv4 Address. . . . . . . . . . . : 172.20.10.2
   Subnet Mask . . . . . . . . . . . : 255.255.255.240
   Default Gateway . . . . . . . . . : 172.20.10.1

2) Ethernet adapter Ethernet: (Access Internal network)

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::e2d4:dd00:69fb:10a4%42
   IPv4 Address. . . . . . . . . . . : 192.168.1.50
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.1.1

above information, please see the cmd command on windows to route this network that I can access these two network without problem (priority is to access internal network then internet)
```



To configure your Windows 10 machine to prioritize accessing your internal network via the Ethernet adapter and then access the internet via the Wi-Fi adapter, you'll need to adjust the routing table using the `route` command.

Here's how you can do it:

### 1. **Add a static route for the internal network:**

The internal network is accessed through the Ethernet adapter (192.168.1.0/24). You'll want to ensure traffic destined for this network goes through the Ethernet adapter.

You can add a static route to your internal network with the following command:

```
cmd
Copy code
route add 192.168.1.0 mask 255.255.255.0 192.168.1.1 metric 1
```

### 2. **Change the default route priority:**

Windows uses metrics to determine the priority of routes. Lower metric values have higher priority. Since you want to prioritize the internal network, ensure the metric for the Ethernet route is lower than for the Wi-Fi route.

First, change the metric of the Wi-Fi route (internet):

```
cmd
Copy code
route change 0.0.0.0 mask 0.0.0.0 172.20.10.1 metric 20 if 16
```

Then, ensure the Ethernet route has a lower metric (e.g., 1):

```
cmd
Copy code
route change 0.0.0.0 mask 0.0.0.0 192.168.1.1 metric 10 if 42
```

### 3. **Ensure persistent routing:**

If you want these routes to persist after a reboot, you can add the `-p` flag to the `route add` commands:

```
cmdCopy coderoute -p add 192.168.1.0 mask 255.255.255.0 192.168.1.1 metric 1
route -p change 0.0.0.0 mask 0.0.0.0 172.20.10.1 metric 20 if 16
route -p change 0.0.0.0 mask 0.0.0.0 192.168.1.1 metric 10 if 42
```

### Explanation:

- `route add`: Adds a new route to the routing table.
- `route change`: Modifies an existing route.
- `-p`: Makes the route persistent across reboots.
- `metric`: Specifies the metric (priority) of the route, with a lower number meaning higher priority.
- `if`: Specifies the interface number. Replace `16` and `42` with the correct interface index numbers if they differ on your system.

### Note:

You can verify the interface index numbers by running:

```
cmd
Copy code
route print
```

This command will display the list of all routes and their associated interfaces.

By following these steps, your machine will prioritize accessing the internal network via Ethernet while still being able to access the internet through Wi-Fi.
