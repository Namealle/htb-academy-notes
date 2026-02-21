# Introduction To Networking

### Introduction
- A lot of things such us IP phones, Workstations and most importantly printers should be on their own network. Printers are one of the biggest exploitation vector. It is possible to upload a shell to the printer and then get a reverse shell once someone connected to it. In order to manage it the printer should not be able to talk to the internet and Printer should not initiate connection to workstations.

### Network Structure
- WAN, LAN, WLAN, VPN
	- WAN - is internet
	- LAN - is your local network
	- WLAN - is same as LAN but accessible wireless
	- VPN - Connects multiple network sites to one LAN
- A proxy is a device or a service which sits in the middle and inspect the content of the traffic.
- Difference between proxy and Gateway is that Gateway only forwards the traffic without inspecting it.
- Proxy or Forward Proxy protects client when Reverse Proxy protects server. Also Forward Proxy controls outgoing traffic and Reverse Proxy controls incoming traffic
### Networking Workflow
- TCP/IP - is often general term for network and consist of 4 layers:
	- 4. Application
	- 3. Transport
	- 2. Internet
	- 1. Link
- ISO/OSI model consist of 7 layers
	- 7. Application
	- 6. Presentation
	- 5. Session
	- 4. Transport
	- 3. Network
	- 2. Data Link
	- 1. Physical

### Addressing
- IPv4 Address consist of 4 octet. Each octet consist of `8 bits`.
	- To find the octet values you need to add values of bits.
- Finally understood what does 802.xx MAC address means
	- IEEE 802.3 - Ethernet
	- IEEE 802.15 - Bluetooth
	- IEEE 802.11 - WLAN
- Several MAC Address attack vectors
	- MAC Spoofing
	- MAC Flooding
	- MAC address filtering
- Learned how to read and translate Binary to Decimal and Hex Decimal.
	- To translate numbers from Binary to Hex or Decimal, You should understand that binary number consist of `8 bits` for example `11110000` will be 240 to understand it table below will be very helpful.

| 128 | 64  | 32  | 16  | 8   | 4   | 2   | 1   |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2^7 | 2^6 | 2^5 | 2^4 | 2^3 | 2^2 | 2^1 | 2^0 |
| 1   | 1   | 1   | 1   | 0   | 0   | 0   | 0   |
After adding `128+64+32+16` you get 240.
Now in order to translate `11110000` to Hex the table below will be helpful

| 1   | 1   | 0001 |
| --- | --- | ---- |
| 2   | 2   | 0010 |
| 3   | 3   | 0011 |
| 4   | 4   | 0100 |
| 5   | 5   | 0101 |
| 6   | 6   | 0110 |
| 7   | 7   | 0111 |
| 8   | 8   | 1000 |
| 9   | 9   | 1001 |
| 10  | A   | 1010 |
| 11  | B   | 1011 |
| 12  | C   | 1100 |
| 13  | D   | 1101 |
| 14  | E   | 1110 |
| 15  | F   | 1111 |
### Pitfalls
 - There are 3 different main types of VPN
	- Site-To-Site VPN - which connects full network
	- Remote Access VPN - connect individual client to the network
		- Split-Tunnel - only specific traffic goes through VPN
		- Full-Tunnel - all traffic goes thought VPN
	- SSL VPN - browser based VPN (just like HTB Pwnbox)
- `10.0.0.0/8`, `172.16.0.0/12` and `192.168.0.0/16` are private IP ranges that should not be routed on the public internet
- Difference between Proxy and a gateway
	- Proxy: sits in the middle and inspects application-layer traffic (Layer 7)
	- Gateway: Just routes and forwards traffic without inspecting it.
- Fully meshed network:
	- Every single device connected to every other device
	- No central router/switch
	- Very expensive and extremely hard to manage
	- Formula for calculation: n(n-1)/2
- Topology:
	- Star topology: All devices connected to one central point router or switch. If the router fails entire network goes down
	- Ring topology: Devices connected in a circle. Data flows in one direction
- **ARP (address Resolution Protocol)**
	- Operates at Layer 2 (Data Link)
	- Maps IP addresses to MAC addresses on local network
	- Device broadcasts: "Who has IP X.X.X.X ? Send me your MAC address"
	- If no response = IP does not exist or device is offline
- Encapsulation - the process where each OSI layer adds its own header to the data as it moves down the stack before transmission
- MAC addresses:
	- 48 bits in hexadecimal: AA:BB:CC:DD:EE:FF
	- Operates at Layer 2 (Data Link)
	- First 24 bits = OUI (Manufactures ID)
	- Last 24 bits = device-specific identifier
- MAC = 48 bits
- IPv4 = 32 bits
- IPv6 = 128 bits
- C2 (Command and Control):
	- Communication channel between attacker and malware
	- Attacker sends commands, malware sends data back
- DNS Tunneling:
	- Hiding C2 traffic inside DNS queries
	- Port 53 is rarely blocked, so it bypasses firewalls
	- Detection: high volume of DNS queries to non-standard servers
- SOC DNS Monitoring
	- Detect malware C2
	- Detect tunneling
	- Detect phishing
	- Detect exfiltration
- Hashing vs Encryption:
	- Encryption: Reversible with key
	- Hashing: One-way, irreversible
	- Can't "decrypt" hash - info is lost
	- To crack hash: guess password, hash it, compare
- Default Gateway:
	- Router that forwards traffic to external networks
	- Exit point from local network
	- Wrong gateway = local works, internet/external fails
