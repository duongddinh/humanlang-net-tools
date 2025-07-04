# humanlang-net-tools

A collection of basic network security and scanning tools implemented in HumanLang. This repository provides scripts for port scanning, custom packet crafting, network sniffing, and ARP scanning/spoofing, designed primarily for educational and learning purposes in network security.

---

## **Important Note:**

This project is implemented using  [humanlang](https://github.com/duongddinh/humanlang.git), my custom programming language.

---

## Network Capabilities of HumanLang:

HumanLang is designed with built-in primitives for various networking operations, making it particularly suitable for network-focused tasks. Currently, it supports:

* **Port Scanning**: Identify open ports on target hosts.
* **Custom Packet Crafting**: Create and send custom IP, TCP, ARP, and ICMP packets with specified layers and fields.
* **Network Sniffing**: Capture and analyze network traffic on a specified interface with BPF filters.
* **ARP Scanning**: Discover active hosts on a local network using ARP requests.
* **ARP Spoofing/Restoration**: Tools to perform and revert ARP cache poisoning.
* **Ping and Traceroute**: Basic network diagnostic commands.
* **HTTP GET Requests**: Perform simple HTTP GET requests.

---

## How to Use:

To run these tools, you'll first need to set up the HumanLang environment.

1.  **Create a directory and clone the HumanLang repository:**
    ```bash
    mkdir humanlang-prog
    cd humanlang-prog
    git clone https://github.com/duongddinh/humanlang.git
    ```

2.  **Install HumanLang:**
    Navigate into the cloned `humanlang` directory and install it in editable mode:
    ```bash
    cd humanlang-prog
    pip install -e .
    ```

3.  **Run the network tools:**
    Once HumanLang is installed, you can run any of the `.human` scripts directly from the `humanlang-prog` directory (or wherever you cloned `humanlang-net-tools`):
    ```bash
    # Example for sniff.human assuming it's in the same directory where you run this
    humanlang sniff.human
    humanlang scan.human
    humanlang arp.human
    ```

---

## Detailed Instructions for Networking Capabilities:

Here's how to use the core networking functionalities within your HumanLang scripts, as demonstrated in `sniff.human`, `scan.human`, and `arp.human`.

**Prerequisites for Network Operations:**
Most network operations (like port scanning, ARP scanning, sending raw packets, and sniffing) require **root or administrator privileges**. You will likely need to run your HumanLang scripts with `sudo` on Linux/macOS or with an elevated command prompt on Windows.

### 1. Port Scanning:
The `perform a port scan on` command allows you to check for open ports.
* **Syntax:** `Perform a port scan on [target_host] for ports "[ports_list]" and store the results in [variable_name].`
* **`[target_host]`**: Can be an IP address or hostname (e.g., `"192.168.1.1"` or `"example.com"`).
* **`[ports_list]`**: A comma-separated string of ports (e.g., `"22, 80, 443"`) or a range (e.g., `"1-1024"`).
* **`[variable_name]`**: An `Object` variable where the results (a dictionary of port to status like "Open", "Closed", "Filtered") will be stored.
* **Example from `sniff.human`**:
    ```humanlang
    Perform a port scan on target_host for ports "22, 80, 443, 8080" and store the results in open_ports.
    Set port_results_str to "" + open_ports.
    ```

### 2. Custom Packet Crafting and Sending:
You can create and send custom network packets with specific layers (Ethernet, IP, TCP, ICMP, ARP).
* **Creating a Packet:** `Create a new "Packet" with layers "[layer1]/[layer2]/..." and call it [packet_variable_name].`
    * **Layers**: Use `IP`, `TCP`, `ARP`, `ICMP`, `Ether`.
    * **Example from `sniff.human`**: `Create a new "Packet" with layers "IP/TCP" and call it custom_packet.`
* **Setting Packet Fields:** Use the `set [packet_variable_name]'s [field] to [value]` syntax.
    * **Common fields for `TCP`**: `dport`, `sport`, `flags` ("S" for SYN, "A" for ACK, etc.), `seq`, `ack`.
    * **Common fields for `IP`**: `dst`, `src`, `ttl`, `id`.
    * **Common fields for `Ether`**: `dst`, `src`.
    * **Example from `sniff.human`**:
        ```humanlang
        Set custom_packet's dst to target_host.
        Set custom_packet's dport to 80.
        Set custom_packet's flags to "S".
        ```
* **Sending a Packet:** `Send packet [packet_variable_name] and store the reply in [reply_variable_name].`
    * The `[reply_variable_name]` will contain the Scapy packet object if a reply is received, otherwise `None`.
    * **Example from `sniff.human`**: `Send packet custom_packet and store the reply in packet_reply.`

### 3. Network Sniffing:
Capture live network traffic based on an interface and a BPF (Berkeley Packet Filter) filter.
* **Syntax:** `Start sniffing on interface "[interface_name]" with filter "[bpf_filter]" for [duration_seconds] seconds and store packets in [variable_name].`
* **`[interface_name]`**: Your network interface (e.g., `"en0"` on macOS, `"eth0"` on Linux).
* **`[bpf_filter]`**: A standard BPF filter string (e.g., `"tcp"`, `"udp port 53"`, `"host 192.168.1.1"`).
* **`[duration_seconds]`**: How long to sniff, in seconds.
* **`[variable_name]`**: A `List` variable where summaries of the captured packets will be stored.
* **Example from `sniff.human`**:
    ```humanlang
    Start sniffing on interface "en0" with filter "tcp" for 10 seconds and store packets in captured_packets.
    ```

### 4. ARP Scanning:
Discover hosts on your local network using ARP requests.
* **Syntax:** `Perform an arp scan on [network_cidr] and store the results in [variable_name].`
* **`[network_cidr]`**: Your network in CIDR format (e.g., `"192.168.1.0/24"`).
* **`[variable_name]`**: A `List of Object` where each object in the list will have `ip` and `mac` properties.
* **Example from `scan.human`**:
    ```humanlang
    perform an arp scan on local_network and store the results in discovered_hosts
    ```

### 5. ARP Spoofing/Restoration:
The `arp.human` script provides functions (`get_mac`, `spoof_arp`, `restore_arp`) to perform ARP cache poisoning.
* These are defined as `task`s within `arp.human` and demonstrate how to craft and send specific ARP packets for malicious (or restorative) purposes.
* **`get_mac` task**: Used internally to resolve MAC addresses for a given IP.
* **`spoof_arp` task**: Continuously sends ARP replies to poison the ARP caches of the gateway and target.
* **`restore_arp` task**: Sends correct ARP replies to restore the ARP caches.
* **Example from `arp.human`**:
    ```humanlang
    perform "spoof_arp" with interface, gateway_ip, target_ip.
    ```
    *(Note: This functionality should be used with extreme caution and only on networks where you have explicit permission.)*

### 6. Ping and Traceroute:
Basic network diagnostic tools.
* **Ping:** `perform a ping to [target_host] and store the result in [variable_name]`
* **Traceroute:** `perform a traceroute to [target_host] and store the result in [variable_name]`
    * Both will store a `String` summary of the operation.

### 7. HTTP GET Requests:
Fetch content from a URL.
* **Syntax:** `perform an http get request to [url_expression] and store the result in [variable_name]`
* **`[url_expression]`**: A string representing the URL (e.g., `"https://www.example.com"`).
* **`[variable_name]`**: A `String` variable to store the response body.

---

## Current Status - Alpha Stage (Bugs Ahead!):

Please be aware that HumanLang, and consequently these tools, are currently in an **alpha development stage**. This means there are likely a lot of bugs, and stability is not guaranteed. Features may change, and unexpected behavior is possible. Your patience and feedback are greatly appreciated!

---
