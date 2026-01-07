*This project has been created as part of the 42 curriculum by ekibar.*

# NetPractice

## Description
NetPractice is a general practical exercise designed to introduce the basics of computer networking. The main goal of this project is to configure small-scale networks to ensure they function properly. By completing the levels, I have learned how to configure IP addresses, connect devices through a router, and understand the critical role of a default gateway within a network.

## Instructions

### How to Run
1.  Download the project files and extract them into a folder.
2.  Open the `index.html` file in a web browser.
    * **Note:** It is strongly recommended to use **Google Chrome** or a **Chromium-based** browser, as Firefox is known to block the usage of this tool due to technical constraints.
3.  Enter your intranet login in the training interface to start with your personal configuration.

### Submission
To complete the project, 10 levels must be solved.
1.  For each level, once the network is functioning (all goals are green), click the **"Get my config"** button.
2.  This will download a configuration file for that specific level.
3.  All **10 exported configuration files** (one per level) must be placed at the root of the Git repository.

## Resources

### Networking Concepts
During this project, the following key networking concepts were studied and applied:
* **TCP/IP Addressing:** Understanding IPv4 formats and classes.
* **Subnet Mask:** Calculating network ranges using CIDR notation (e.g., /24, /30) and preventing overlapping subnets.
* **Default Gateway:** Configuring routes to allow devices to communicate outside their local network.
* **Routers and Switches:** Understanding the difference between Layer 2 (Switch) and Layer 3 (Router) devices.
* **Routing Tables:** Manually configuring static routes (Destination, Next Hop) to ensure packets reach their destination and return correctly.

### References & Tutorials
The following resources were used to understand the core concepts and solve the exercises:

* **Cisco Documentation:** Official documentation and guides regarding networking fundamentals, routing protocols, and switching logic were consulted to ensure standard compliance.

* **DeltaNet Academy (YouTube Playlists):**
    * [Networking Playlist 1](https://www.youtube.com/watch?v=pYk18pGdu_c&list=PL966g7O8Fr37rJ_KweisV91jGslj8_IXY)
    * [Networking Playlist 2](https://www.youtube.com/watch?v=Yu8nz0GJpls&list=PL966g7O8Fr34Z-PsTCM1YBzB6COiu0uZp)
    * [Networking Playlist 3](https://www.youtube.com/watch?v=1fTdar_GL9A&list=PL966g7O8Fr34AuPFQjb0v87rmtDApmmK_)

### AI Usage
I used AI tools (Gemini) in this project for the following tasks:
* **Planning:** Used AI to structure my workflow, organize the problem-solving steps for each level, and create a roadmap for learning the necessary concepts.
* **Debugging:** To analyze simulation logs (e.g., "destination does not match any route", "loop detected") and understand why specific configurations failed.
* **Translation:** To clarify specific technical terms and instructions in the subject document.

## Study Notes
Here are my personal notes, debugging tricks, and quick references derived from the learning process.

### 1. TCP/IP Communication Logic (The "Ping-Pong" Rule)
In NetPractice, configuring a route one-way is not enough. Communication is a cycle:
* **Forward Path:** Source (A) -> Router -> Destination (B).
* **Reverse Path:** Destination (B) -> Router -> Source (A).
* **Common Mistake:** If `Goal 1` fails but logs show "Destination IP reached" in the forward way, the issue is usually a missing route in the **Reverse Path**. The destination doesn't know how to send the answer back.

### 2. IP Roles & Constraints
| IP Type | Example (for /24) | Description | NetPractice Rule |
| :--- | :--- | :--- | :--- |
| **Network Address** | `192.168.1.0` | The identifier of the subnet. | - Never assign to an interface. |
| **Broadcast Address** | `192.168.1.255` | Used to communicate with everyone. | - Never assign to an interface. |
| **Usable Range** | `.1` - `.254` | Addresses available for hosts/routers. | + Assign these to interfaces. |
| **Loopback** | `127.0.0.1` | Localhost. Points to the device itself. | - Cannot be used for device-to-device communication. |

### 3. Public vs. Private IPs (The "Internet" Rule)
Routers connecting to the "Internet" cloud must use Public IPs.
* **Private Ranges (Forbidden on Internet Interfaces):**
    * `192.168.x.x`
    * `10.x.x.x`
    * `172.16.x.x` - `172.31.x.x`
* **Error:** "Private subnets not routed over internet" means you assigned a private IP to the WAN interface.

### 4. Device Differences
| Feature | Router (L3) | Switch (L2) |
| :--- | :--- | :--- |
| **Function** | Connects **different** networks. | Connects devices within the **same** network. |
| **Logic** | Uses IP Addresses & Routing Tables. | Uses MAC Addresses (Transparent to IP). |
| **Interface Rules** | Each interface MUST be in a different subnet. | All connected devices belong to the same subnet. |

### 5. Routing Table & Log Analysis
* **Error: "Destination does not match any route"**
    * **Meaning:** The router/host has the packet but doesn't know where to send it.
    * **Fix:** Add a Static Route or Default Route (`0.0.0.0/0`).
* **Error: "Invalid IP address"**
    * **Meaning:** You assigned a Network ID (e.g., .0) or Broadcast ID (e.g., .255) to a device.
    * **Fix:** Change the host IP to a usable number in the range.
* **Error: "Loop detected"**
    * **Meaning:** R1 points to R2, and R2 points back to R1 for the same destination.
    * **Fix:** Check the "Next Hop" of the Default Route on the Internet-facing router.

### 6. Subnetting Quick Calc (Magic Number Method)
**Formula:** `256 - (Last Octet of Mask) = Block Size`

* **/30 (.252):** `256 - 252 = 4` IPs. (2 Usable). *Perfect for Router-to-Router links.*
* **/28 (.240):** `256 - 240 = 16` IPs. (14 Usable).
* **/27 (.224):** `256 - 224 = 32` IPs. (30 Usable).
* **/25 (.128):** `256 - 128 = 128` IPs. (126 Usable).