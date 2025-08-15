Task 8 — VPN Setup, Verification & Report (Kali Linux)

##  Objective
To set up a **VPN connection** on Kali Linux using **ProtonVPN's OpenVPN configuration**, verify that traffic is routed through the encrypted tunnel, capture proof of connection, and document the results.

---

## 🛠 Tools Used
- **Kali Linux**
- **OpenVPN** (CLI client)
- **ProtonVPN Free** (provider)
- `curl` & `dig` (for IP verification)
- `tcpdump` & **Wireshark** (optional encryption proof)
- Screenshot tool (`gnome-screenshot` or `xfce4-screenshooter`)

---

##  Folder Structure (Final GitHub Repo)

Task-8-VPN/
├── README.md
├── configs/
│ └── us-free-1.protonvpn.udp.ovpn # NOT committed to GitHub (contains sensitive info)
├── artifacts/
│ ├── before_ip.txt
│ ├── routes_before.txt
│ ├── if_before.txt
│ ├── vpn_log.txt
│ ├── after_ip.txt
│ ├── routes_after.txt
│ ├── if_after.txt
│ ├── after_disconnect_ip.txt
│ └── vpn_first_traffic.pcap # optional
└── screenshots/
├── before_vpn_ip.png
├── after_vpn_ip.png
└── vpn_connected.png


⚠ **DO NOT commit:**
- `.ovpn` configuration files
- `vpn-auth.txt` (contains ProtonVPN service credentials)

---

##  Step-by-Step Procedure

### 1️⃣ **Baseline Capture (Before VPN)**
Run these commands to record your **real IP**, **network interfaces**, and **routes** before connecting to the VPN.
```bash
cd ~/Task-8-VPN
curl -4 ifconfig.me | tee artifacts/before_ip.txt
dig +short myip.opendns.com @resolver1.opendns.com | tee -a artifacts/before_ip.txt
ip -br a | tee artifacts/if_before.txt
ip route | tee artifacts/routes_before.txt

Take a screenshot from a browser showing your real IP:

    Save as: screenshots/before_vpn_ip.png

2️⃣ Prepare VPN Config

    Place your ProtonVPN .ovpn file in configs/:

configs/us-free-1.protonvpn.udp.ovpn

Create a file with your ProtonVPN service credentials:

nano configs/vpn-auth.txt

Format:

YOUR_SERVICE_USERNAME
YOUR_SERVICE_PASSWORD

Save & secure:

chmod 600 configs/vpn-auth.txt

Edit .ovpn file to use this auth file:

    auth-user-pass vpn-auth.txt

3️⃣ Connect to VPN

cd ~/Task-8-VPN/configs
sudo openvpn --config ./us-free-1.protonvpn.udp.ovpn --verb 3 | tee ../artifacts/vpn_log.txt

Wait for:

Initialization Sequence Completed

Leave this terminal running.

Take a screenshot:

    Save as: screenshots/vpn_connected.png

4️⃣ Verify VPN is Active

Open a new terminal:

cd ~/Task-8-VPN
curl -4 ifconfig.me | tee artifacts/after_ip.txt
dig +short myip.opendns.com @resolver1.opendns.com | tee -a artifacts/after_ip.txt
ip -br a | tee artifacts/if_after.txt
ip route | tee artifacts/routes_after.txt

 The IP should be different and tun1 interface should be present.

Take a screenshot from a browser showing your VPN IP:

    Save as: screenshots/after_vpn_ip.png

5️⃣ (Optional) Prove Encryption

If tun1 exists, capture first 200 packets:

sudo tcpdump -i tun1 -c 200 -w artifacts/vpn_first_traffic.pcap

Open in Wireshark to confirm encrypted packets.
6️⃣ Disconnect VPN

Go back to the OpenVPN terminal and press:

Ctrl + C

Verify real IP is restored:

cd ~/Task-8-VPN
curl -4 ifconfig.me | tee artifacts/after_disconnect_ip.txt

 Verification Proof
Stage	IP Address	Interface	Route Through
Before VPN	Real IP	eth0	ISP Gateway
After VPN	VPN IP	tun1	VPN Gateway
After Disconnect	Real IP	eth0	ISP Gateway
 How VPN Works (Summary)

A VPN (Virtual Private Network) creates an encrypted tunnel between your system and a remote VPN server.

    What it hides: Your real IP & location.

    What it secures: Prevents ISP, hackers, or public Wi-Fi snoopers from reading your traffic.

    Protocols: OpenVPN (TLS), WireGuard, IKEv2/IPsec.

 Screenshots Checklist

before_vpn_ip.png

after_vpn_ip.png

    vpn_connected.png

