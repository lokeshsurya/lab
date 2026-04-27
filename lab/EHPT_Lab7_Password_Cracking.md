# EHPT Lab 7 – Password Cracking
# Tools: Hydra, Hashcat, John the Ripper, Medusa

# ============================================================
# SETUP: Create a vulnerable Flask login app (target server)
# ============================================================

# STEP 1: Install Flask
pip install flask --break-system-packages

# STEP 2: Create the vulnerable app
cat > ~/new/app.py << 'EOF'
from flask import Flask, request, render_template_string

app = Flask(__name__)

# Fixed credentials
USERNAME = "admin"
PASSWORD = "password"

html = '''
<h2>Login Page</h2>
<form method="POST">
  Username: <input name="username"><br><br>
  Password: <input type="password" name="password"><br><br>
  <input type="submit" value="Login">
</form>
'''

@app.route('/', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        user = request.form.get('username')
        pwd = request.form.get('password')
        if user == USERNAME and pwd == PASSWORD:
            return "Welcome Admin"
        else:
            return "Invalid credentials"
    return html

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
EOF

# STEP 3: Run the target server (in one terminal)
mkdir -p ~/new
cd ~/new
sudo python3 app.py &
# Server runs at http://127.0.0.1:80

# Verify it's running:
sudo netstat -tuln | grep :80

# ============================================================
# HYDRA – Brute force attacks
# ============================================================

# STEP 4: Hydra on HTTP POST form (web login)
hydra -l admin -P /usr/share/wordlists/rockyou.txt 127.0.0.1 \
  http-post-form "/:username=^USER^&password=^PASS^:Invalid credentials"

# STEP 5: Hydra on SSH
sudo service ssh start    # start SSH first
hydra -l admin -P /usr/share/wordlists/rockyou.txt 127.0.0.1 ssh -t 4

# STEP 6: Hydra on FTP
sudo service vsftpd start    # start FTP first
netstat -tuln | grep :21
hydra -l admin -P /usr/share/wordlists/rockyou.txt 127.0.0.1 ftp

# ============================================================
# MEDUSA – Brute force tool
# ============================================================

# STEP 7: Medusa on SSH
medusa -h 127.0.0.1 -u admin -P /usr/share/wordlists/rockyou.txt -M ssh

# STEP 8: Medusa on HTTP
medusa -h 127.0.0.1 -u admin -P /usr/share/wordlists/rockyou.txt -M http

# ============================================================
# JOHN THE RIPPER – Hash cracking
# ============================================================

# STEP 9: Create a hash file to crack
echo "admin:$(openssl passwd -1 'password')" > hashes.txt
cat hashes.txt

# STEP 10: Crack with John
john hashes.txt
john hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt
john --show hashes.txt

# STEP 11: Crack /etc/shadow (local users)
sudo unshadow /etc/passwd /etc/shadow > unshadowed.txt
john unshadowed.txt --wordlist=/usr/share/wordlists/rockyou.txt

# ============================================================
# HASHCAT – GPU-based hash cracking
# ============================================================

# STEP 12: Generate MD5 hash
echo -n "password" | md5sum
# Result: 5f4dcc3b5aa765d61d8327deb882cf99

# STEP 13: Crack MD5 with Hashcat (mode 0 = MD5)
hashcat -m 0 5f4dcc3b5aa765d61d8327deb882cf99 /usr/share/wordlists/rockyou.txt

# STEP 14: Crack SHA1 (mode 100)
echo -n "password" | sha1sum
# Result: 5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8
hashcat -m 100 5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8 /usr/share/wordlists/rockyou.txt

# STEP 15: Show cracked results
hashcat -m 0 5f4dcc3b5aa765d61d8327deb882cf99 /usr/share/wordlists/rockyou.txt --show

# Conclusion:
# Password cracking was performed using Hydra (HTTP/SSH/FTP), Medusa (SSH/HTTP),
# John the Ripper (hash files), and Hashcat (MD5/SHA1 hashes).
# Dictionary attack using rockyou.txt successfully cracked weak passwords.
# This demonstrates the importance of strong, unique passwords and account lockout policies.
