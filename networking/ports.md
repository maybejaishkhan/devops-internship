# Ports

> In computer networking, a **port** is a **logical endpoint** for **communication between devices** over a network. Think of it as a **doorway through which data enters or leaves** a device, especially a server. It is a function of the **Transport Layer** of the OSI model.

Each port is associated with a **specific service or application** and is used to differentiate traffic on the same IP address. It is typically written after the IP Address or Domain and separated with a colon, for example: `ip:port` or `domain:port`.

They are stored as **16-bit unsigned integers** ($2^{16} = 65536$ possible values). In total, there are 65536 ports. Importantly, the port numbering space is distinct for TCP and UDP; thus, technically there are 65536 TCP ports and 65536 UDP ports.

These port ranges are standardized by the **Internet Assigned Numbers Authority (IANA)**:

  * **Well-Known Ports** (`0-1023`): Reserved for standard, widely used services (e.g., HTTP/HTTPS, SSH, FTP). These are often associated with system processes.
  * **Registered Ports** (`1024-49151`): Can be registered by organizations or vendors for specific applications, though they are not controlled as strictly as well-known ports.
  * **Dynamic/Private (Ephemeral) Ports** (`49152-65535`): Used for temporary, client-side connections. The operating system dynamically assigns these ports for outbound communications, such as browser sessions or making an API call.

## Common and Default Ports

1. **Connection Protocols**

   * TCP `80/443`: HTTP/HTTPS (Web Browse)
   * TCP `22`: SSH (Secure Shell)
   * TCP `23`: Telnet (Unencrypted Remote Access)
   * TCP `3389`: RDP (Remote Desktop Protocol)
   * TCP `5900`: VNC (Virtual Network Computing)

2. **File Transfer Protocols**

   * TCP `20/21`: FTP (File Transfer Protocol - Data/Control)
   * TCP `989/990`: FTPS (FTP Secure - Data/Control)
   * UDP `69`: TFTP (Trivial File Transfer Protocol)
   * TCP `22`: SFTP (SSH File Transfer Protocol - via SSH)
   * TCP `445`: SMB (Server Message Block - Windows File Sharing)

3. **Email Protocols**

   * TCP `25/465`: SMTP/SMTPS (Simple Mail Transfer Protocol - Sending Email)
   * TCP `110/995`: POP3/POP3S (Post Office Protocol 3 - Retrieving Email)
   * TCP `143/993`: IMAP/IMAPS (Internet Message Access Protocol - Retrieving Email)

4. **Network Services**

    * TCP/UDP `53`: DNS (Domain Name System)
    * UDP `67/68`: DHCP (Dynamic Host Configuration Protocol - Server/Client)
    * TCP/UDP `161/162`: SNMP (Simple Network Management Protocol)
    * UDP `520`: RIP (Routing Information Protocol)
    * UDP `514`: Syslog (System Logging)

5. **Core Services**

   * UDP `123`: NTP (Network Time Protocol)
   * TCP/UDP `137/138/139`: NetBIOS (Network Basic Input/Output System)
   * TCP `135`: RPC (Remote Procedure Call)

6. **Common Application Ports (Examples)**

   * `3000`: Node.js / Grafana
   * `3306`: MySQL / MariaDB
   * `5000`: Flask
   * `5432`: PostgreSQL
   * `6379`: Redis
   * `8080/8443`: HTTP/HTTPS (Alternative Web Ports)
   * `9000`: PHP-FPM
   * `9090`: Prometheus
   * `27017`: MongoDB

## Port Mapping (Port Forwarding)

Port mapping, often called port forwarding, is the process of routing external network traffic from a specific port on one device (e.g., a router's public IP) to a specific internal service on another device (e.g., a server on a private network) via a specified port number. This is critical when:

  * A device or service is **behind a Network Address Translation (NAT) device**.
  * You're running **multiple services on the same machine** but need to expose them uniquely.
  * You're using **virtualization or containers** which typically isolate services from the host network.

**Practical Examples:**

1. **Home Network (NAT Router)**: Say your home has a web server on `192.168.1.10:80` and your router has a public IP `203.0.113.5`. To make the web server accessible from the internet:

      * Set up a **port forwarding rule** on your router: `WAN 203.0.113.5:8080 → LAN 192.168.1.10:80`
      * This allows external users to visit `http://203.0.113.5:8080` and reach your internal web server.

2. **Docker Containers**: By default, Docker **isolates containers** with their own virtual network. Services inside a container cannot be accessed from outside unless you explicitly expose or map ports.

      * `docker run -p <hostPort>:<containerPort> image_name`
      * This can be similarly configured in Docker Compose using the `ports` directive.

3. **Virtual Machines (VMs)**: VMs often reside on a **host-only** or **NAT** network type, requiring port mapping for external access.

      * This can often be configured graphically through VM management software.
      * **KVM (using `libvirt` and `iptables`):**
          * Using `iptables`:

            ```bash
            sudo iptables -t nat -A PREROUTING -p tcp --dport 2222 -j DNAT --to 192.168.122.100:22
            sudo iptables -A FORWARD -p tcp -d 192.168.122.100 --dport 22 -j ACCEPT
            ```

          * Or by editing the `libvirt` network XML configuration:

            ```xml
            <network>
              <name>default</name>
              <forward mode='nat'>
                <port start='2222' end='2222' protocol='tcp'/>
                </forward>
            </network>
            ```

## Ephemeral Ports (Dynamic Ports)

When a client application initiates a connection *to* a service (e.g., Browse a website, fetching data from an API, sending an email), the operating system assigns a **random high-numbered port** (from the ephemeral range, typically `49152–65535`) as the **source port** for that outbound connection.

**Example Connection:** Say you're Browse `https://example.com`:

```
    Client IP: 192.168.0.10, assigned ephemeral source port 51234
    Server IP: 93.184.216.34, destination port 443 (HTTPS)
```

The complete connection is identified by a **socket pair** (or a 5-tuple for stateful connections): `Client IP:Client Port → Server IP:Server Port`. In this case: `192.168.0.10:51234 → 93.184.216.34:443`.

This mechanism allows your system to keep multiple concurrent connections open simultaneously — each outbound connection gets a unique source port, preventing conflicts.

> **Note on NAT and Ephemeral Ports**: Network Address Translation (NAT) devices track connections by mapping internal `IP:port` pairs to public `IP:port` pairs (often rewriting ephemeral source ports). This crucial function allows many clients on a local area network (LAN) to share a single public IP address when communicating with the internet.

**Managing Ephemeral Ports on Linux:**

```bash
# Check the current ephemeral port range
cat /proc/sys/net/ipv4/ip_local_port_range

# Example output: 32768 60999 (default varies by distribution)

# Change the ephemeral port range (e.g., for specific application needs)
sudo sysctl -w net.ipv4.ip_local_port_range="10000 65000"

# To make this change persistent across reboots, add it to /etc/sysctl.conf
# net.ipv4.ip_local_port_range = 10000 65000
```