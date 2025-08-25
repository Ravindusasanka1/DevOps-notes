

````markdown

Understanding networking fundamentals is critical for DevOps engineers.  
This note covers **Switching, Routing, Default Gateway, and DNS configurations in Linux**.

---

## 1. Switching
- **What it is**: A switch is a Layer 2 device that connects devices within the same network (LAN).
- **Key concept**: Uses **MAC addresses** to forward frames between devices.
- **Example**: When your laptop talks to a server in the same subnet, the switch delivers traffic directly.

**DevOps Relevance**
- Kubernetes nodes, Docker hosts, and servers inside a data center rely on switches for communication.
- Understanding switching helps troubleshoot issues like “pod can’t reach service” inside the same subnet.

---

## 2. Routing
- **What it is**: Routing is the process of moving packets **between different networks**.
- **Key concept**: Routers operate at **Layer 3 (IP)**, making decisions based on **IP addresses**.
- **Example**: Your local network `192.168.1.0/24` sending traffic to the internet.

**Linux Example**
```bash
# Show routing table
ip route show

# Add a static route
sudo ip route add 10.0.0.0/24 via 192.168.1.1
````

---

## 3. Default Gateway

* **What it is**: The **exit door** of your network — when a packet doesn’t know where to go, it’s sent to the default gateway (usually a router).
* **Example**: Your PC at `192.168.1.10` sends traffic to `8.8.8.8`. Since `8.8.8.8` isn’t in the local network, traffic goes to the default gateway.

**Linux Example**

```bash
# Check default gateway
ip route | grep default

# Add/change default gateway
sudo ip route add default via 192.168.1.1
```

---

## 4. DNS Configuration on Linux

* **What it is**: DNS resolves human-friendly names (`google.com`) into IP addresses (`142.250.190.14`).
* **Files to know**:

  * `/etc/resolv.conf` → defines nameservers.
  * `/etc/hosts` → local overrides.

**Linux Examples**

```bash
# Check current DNS configuration
cat /etc/resolv.conf

# Add Google DNS temporarily
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# Test DNS resolution
dig openai.com
nslookup github.com
```

---

## 🔑 Key Takeaways

* **Switching** → Communication inside same network (MAC based).
* **Routing** → Communication between networks (IP based).
* **Default Gateway** → Router that forwards unknown traffic outside your subnet.
* **DNS** → Name-to-IP translation, configured in `/etc/resolv.conf`.

---

## ⚡ Quick Interview Q\&A

**Q1. What’s the difference between a switch and a router?**

* Switch connects devices within the same network (MAC-based).
* Router connects different networks (IP-based).

**Q2. What happens if DNS is misconfigured in Linux?**

* You can ping by IP but not by hostname.

**Q3. How to view the Linux routing table?**

* `ip route show`

**Q4. Why does every host need a default gateway?**

* To send traffic outside its local subnet.

## **Networking Basics lecture**


# 📝 Linux Networking Commands 

## 🔹 1. Check Network Interfaces

```bash
ip link
```

* Lists all available network interfaces on the system.
* Shows their state (UP/DOWN) and configuration details.
* Useful for identifying available interfaces before assigning IPs.

---

## 🔹 2. View IP Addresses

```bash
ip addr
```

* Displays all assigned IP addresses on the system.
* Useful for checking current interface configurations.

---

## 🔹 3. Assign an IP Address to an Interface

```bash
ip addr add 192.168.1.10/24 dev eth0
```

* Assigns the IP `192.168.1.10` with subnet mask `/24` to the interface `eth0`.
* Replace `eth0` with the actual interface name.

✅ Example:

* If you want your system to join the `192.168.1.0/24` network:

```bash
ip addr add 192.168.1.100/24 dev eth0
```

---

## 🔹 4. View Routing Table

```bash
ip route
```

* Shows current kernel routing table.
* Helps check default gateways and available routes.

---

## 🔹 5. Add a Route

```bash
route add -net 192.168.1.0/24 via 192.168.2.1
```

* Adds a route to reach the `192.168.1.0/24` network **via gateway** `192.168.2.1`.

✅ Example:
If your system is in the `192.168.2.0/24` network but needs access to `192.168.1.0/24`:

```bash
route add -net 192.168.1.0/24 gw 192.168.2.1
```

---

## 🔹 6. Enable IP Forwarding (for Routing / NAT)

```bash
cat /proc/sys/net/ipv4/ip_forward
```

* Checks if IP forwarding is enabled.
* Output:

  * `0` → Disabled (default).
  * `1` → Enabled (system can route packets between interfaces).

✅ To enable temporarily:

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

✅ To make it permanent:
Edit `/etc/sysctl.conf` and set:

```conf
net.ipv4.ip_forward = 1
```

Then apply:

```bash
sysctl -p
```

---

## 🔹 7. Legacy `route` Command

```bash
route
```

* Displays current routing table (older command, replaced by `ip route`).
* Still useful for quick checks in legacy systems.

---

# 🚀 DevOps Use Cases

* **Troubleshooting connectivity** → check interfaces and routes.
* **Configuring static IPs** → `ip addr add`.
* **Custom routing** → `route add` for multi-network environments.
* **NAT/Load Balancing** → enable IP forwarding for packet routing.

---
## **Networking basics lab**


# 📝 Linux Networking Lab Notes (Jump Host + App Servers)

## 🔹 1. Checking Interfaces and Routes

```bash
ip link
```

* Lists all interfaces:

  * `lo` → loopback
  * `eth0` → `172.16.238.0/24` network
  * `eth1` → `172.17.0.0/16` network

```bash
route
```

* Shows routing table:

  * Default route → `_gateway` via `eth0`
  * `172.16.238.0/24` → directly reachable via `eth0`
  * `172.17.0.0/16` → directly reachable via `eth1`

---

## 🔹 2. Assigning IPs to App Servers

Each server (`app01` → `app04`) is reachable only via jump host.

✅ Correct steps:

```bash
ssh app01
sudo ip addr add 172.16.238.15/24 dev eth0
exit

ssh app02
sudo ip addr add 172.16.238.16/24 dev eth0
exit

ssh app03
sudo ip addr add 172.16.239.15/24 dev eth0
exit

ssh app04
sudo ip addr add 172.16.239.16/24 dev eth0
exit
```

⚠️ **Mistake noticed**: At one point you added **all four IPs on app01**:

```bash
sudo ip addr add 172.16.238.15/24 dev eth0
sudo ip addr add 172.16.238.16/24 dev eth0
sudo ip addr add 172.16.239.15/24 dev eth0
sudo ip addr add 172.16.239.16/24 dev eth0
```

👉 That was wrong. Each IP belongs to a **different server**, not all on app01. You later fixed this by re-logging into app03 and app04 and assigning the correct IPs.

---

## 🔹 3. Jump Host IP Management

* Jump host already has:

  ```bash
  inet 172.16.238.10/24 scope global eth0
  ```
* You **should not change this IP** (instructions said so).
* Your attempt:

  ```bash
  sudo ip addr add 172.16.xxx.xxx/24 dev eth0
  ```

  failed because `xxx` is invalid — IPs must be concrete (e.g., `172.16.239.10/24`).

---

## 🔹 4. Routing Attempts

You tried:

```bash
sudo ip addr add 172.16.239.10/24 dev eth0
sudo ip route add 172.16.239.0/24 via 172.16.238.10
sudo ip route add 172.16.238.0/24 via 172.16.239.10
```

* Adding `172.16.239.10/24` to jump host → means you gave jump host an address in the new subnet. Valid approach if you want jump host to talk directly with `172.16.239.0/24`.
* But route commands failed with:

  ```
  RTNETLINK answers: File exists
  ```

  Because both `172.16.238.0/24` and `172.16.239.0/24` are already directly reachable — Linux adds connected networks automatically, so no manual route is needed.

---

## 🔹 5. Wrong Commands to Remember

* `check` and `ok` → not valid Linux commands (system complained: `command not found`).
* You don’t need to run such commands inside app servers — the validation is done by the external checker.

---

# ✅ Final Correct Setup

* **Jump Host**

  * `172.16.238.10/24` (unchanged)
* **App Servers**

  * app01 → `172.16.238.15/24`
  * app02 → `172.16.238.16/24`
  * app03 → `172.16.239.15/24`
  * app04 → `172.16.239.16/24`

Linux routing auto-handles subnet reachability. No need to add custom routes unless there’s a router in between.

👉 These notes now capture what worked, what was wrong, and why.

---
## **DNS in linux lecture**
--!!!
## **DNS lab**

---

# 📝 Linux DNS & Name Resolution Notes

## 🔹 1. `/etc/resolv.conf`

```bash
cat /etc/resolv.conf
```

Output:

```conf
search stratos.xfusioncorp.com
nameserver 127.0.0.11
options ndots:5
```

* **search stratos.xfusioncorp.com** → default search domain. If you type `app01`, the system tries `app01.stratos.xfusioncorp.com`.
* **nameserver 127.0.0.11** → special DNS resolver used inside Docker/Podman containers.
* **options ndots:5** → if a name has fewer than 5 dots, system appends search domain.

✅ You later added:

```bash
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
echo "search yahoo.com" | sudo tee -a /etc/resolv.conf
```

* This appends Google’s DNS (`8.8.8.8`) and adds a new search domain (`yahoo.com`).
* Now resolver will also try `*.yahoo.com` when hostnames are incomplete.

---

## 🔹 2. `/etc/nsswitch.conf`

```bash
/etc/nsswitch.conf
```

* Gave error `Permission denied` because you tried to **execute** the file.
* Correct way:

```bash
cat /etc/nsswitch.conf
```

* This file controls **name resolution order** (e.g., `files dns` means check `/etc/hosts` first, then DNS).

---

## 🔹 3. `/etc/hosts` Local Overrides

```bash
echo "127.0.0.1 www.google.com" | sudo tee -a /etc/hosts
```

* Maps `www.google.com` → `127.0.0.1`.
* Local `/etc/hosts` entries always override DNS.
* Meaning: when you `ping www.google.com`, it will resolve to **localhost** instead of Google’s real IP.
  👉 This is often used for testing or blocking domains.

---

## 🔹 4. Testing Resolution inside Container

```bash
docker exec jump_host bash -c "getent hosts news"
```

* `getent hosts <hostname>` → tests resolution according to **nsswitch.conf**.
* Error came up due to Podman/Docker rootless mode:

  ```
  cannot set up namespace using "/usr/bin/newuidmap": Operation not permitted
  ```
* This isn’t DNS-related — it’s a **container runtime issue** (rootless Podman trying to map UIDs).

---

# ✅ Key Takeaways

1. **/etc/resolv.conf**

   * Controls DNS servers and search domains.
   * Changes are temporary inside containers (often overwritten by Docker).
   * Use `/etc/docker/daemon.json` or Podman configs for persistent DNS.

2. **/etc/nsswitch.conf**

   * Defines resolution order: e.g., `files dns` means check `/etc/hosts` first, then DNS.

3. **/etc/hosts**

   * Overrides DNS lookups.
   * Useful for testing or redirecting domains.

4. **getent hosts**

   * Best way to test resolution according to system rules.
   * Unlike `ping`, it shows what resolver chain (hosts/DNS) is returning.

---

👉 In your case, you redirected `www.google.com` to localhost, added `8.8.8.8` as DNS, and modified the search domain. That means:

* Typing `ping test` → system tries `test.yahoo.com` or `test.stratos.xfusioncorp.com`.
* `ping www.google.com` → resolves to `127.0.0.1`.
* `dig something.com` → goes to `8.8.8.8` if local DNS fails.

---






