# EHPT Lab 2 – Open Source Intelligence (OSINT)
# Target for practice: use dypu.ac.in or any public domain

# STEP 1: Install OSINT tools
sudo apt install whois dnsutils curl exiftool -y
pip install holehe --break-system-packages 2>/dev/null || true

# STEP 2: WHOIS lookup (domain registration info)
whois hackthissite.org
whois dypu.ac.in

# STEP 3: DNS lookup – A record (IP address)
dig dypu.ac.in A
nslookup dypu.ac.in

# STEP 4: DNS lookup – MX records (mail servers)
dig dypu.ac.in MX

# STEP 5: DNS lookup – NS records (name servers)
dig dypu.ac.in NS

# STEP 6: DNS lookup – TXT records (SPF, verification)
dig dypu.ac.in TXT

# STEP 7: Reverse DNS lookup
dig -x 184.168.99.132

# STEP 8: Find subdomains using subfinder or dnsdumpster
sudo apt install subfinder -y 2>/dev/null || true
subfinder -d dypu.ac.in 2>/dev/null || echo "Visit https://dnsdumpster.com manually"

# STEP 9: Extract metadata from an image (EXIF data)
# Download a sample image first
wget -O sample.jpg "https://www.gstatic.com/webp/gallery/1.jpg" 2>/dev/null
exiftool sample.jpg

# STEP 10: theHarvester – email/subdomain gathering
sudo apt install theharvester -y
theHarvester -d dypu.ac.in -b bing 2>/dev/null || theHarvester -d dypu.ac.in -b google

# STEP 11: Shodan (web-based — paste this in browser)
echo "Visit: https://www.shodan.io/search?query=dypu.ac.in"
echo "Visit: https://dnsdumpster.com and search dypu.ac.in"

# STEP 12: Save all OSINT results
echo "=== WHOIS ===" > osint_results.txt
whois dypu.ac.in >> osint_results.txt
echo "=== DNS A ===" >> osint_results.txt
dig dypu.ac.in A >> osint_results.txt
echo "=== MX ===" >> osint_results.txt
dig dypu.ac.in MX >> osint_results.txt
echo "=== TXT ===" >> osint_results.txt
dig dypu.ac.in TXT >> osint_results.txt
cat osint_results.txt

# Conclusion:
# OSINT tools (whois, dig, theHarvester, exiftool) were used to gather
# publicly available info about a target without direct interaction.
# DNS records, WHOIS data, subdomains, and image metadata were extracted.
