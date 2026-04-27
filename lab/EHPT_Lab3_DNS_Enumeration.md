# EHPT Lab 3 – DNS Enumeration
# Exactly as done in the lab manual (dypu.ac.in used as target)

# STEP 1: Install tools
sudo apt install dnsutils dnsenum dnsrecon -y

# STEP 2: Basic dig – A record (IPv4 address)
dig dypu.ac.in A

# STEP 3: dig – AAAA record (IPv6)
dig dypu.ac.in AAAA

# STEP 4: dig – MX records (mail servers)
dig dypu.ac.in MX

# STEP 5: dig – NS records (name servers)
dig dypu.ac.in NS

# STEP 6: dig – TXT records (SPF / verification strings)
dig dypu.ac.in TXT

# STEP 7: dig – PTR (reverse lookup)
dig dypu.ac.in PTR

# STEP 8: dig – CNAME
dig dypu.ac.in CNAME

# STEP 9: dig – SOA (Start of Authority)
dig dypu.ac.in SOA

# STEP 10: nslookup (alternative to dig)
nslookup dypu.ac.in
nslookup -type=MX dypu.ac.in
nslookup -type=NS dypu.ac.in
nslookup -type=TXT dypu.ac.in

# STEP 11: Zone Transfer attempt (usually blocked but try)
# Replace ns1.dypu.ac.in with actual nameserver from dig NS output
dig axfr dypu.ac.in @ns17.domaincontrol.com 2>/dev/null || echo "Zone transfer denied (expected)"

# STEP 12: dnsenum – full DNS enumeration
dnsenum dypu.ac.in

# STEP 13: dnsrecon – comprehensive recon
dnsrecon -d dypu.ac.in

# STEP 14: Subdomain brute force
dnsrecon -d dypu.ac.in -t brt

# STEP 15: Save all results
echo "=== DNS ENUMERATION RESULTS ===" > dns_enum.txt
echo "--- A Record ---" >> dns_enum.txt
dig dypu.ac.in A >> dns_enum.txt
echo "--- MX Record ---" >> dns_enum.txt
dig dypu.ac.in MX >> dns_enum.txt
echo "--- NS Record ---" >> dns_enum.txt
dig dypu.ac.in NS >> dns_enum.txt
echo "--- TXT Record ---" >> dns_enum.txt
dig dypu.ac.in TXT >> dns_enum.txt
echo "--- SOA Record ---" >> dns_enum.txt
dig dypu.ac.in SOA >> dns_enum.txt
cat dns_enum.txt

# Conclusion:
# DNS enumeration was performed using dig, nslookup, dnsenum, and dnsrecon.
# A, AAAA, MX, NS, TXT, SOA, CNAME records were extracted for dypu.ac.in.
# Zone transfer was attempted but denied (secure configuration).
# These records reveal mail servers, IPs, subdomains, and DNS infrastructure.
