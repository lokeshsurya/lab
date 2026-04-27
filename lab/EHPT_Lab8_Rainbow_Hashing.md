# EHPT Lab 8 – Rainbow Tables & Hashing
# Tools: hashid, rtgen/rainbowcrack, bcrypt, argon2

# ============================================================
# PART A: GENERATE AND IDENTIFY HASHES
# ============================================================

# STEP 1: Install tools
sudo apt install hashid hashcat rainbowcrack -y
pip install bcrypt argon2-cffi --break-system-packages

# STEP 2: Generate MD5 hash
echo -n "lokeshhhh" | md5sum
# Output: a8ba8931efd321b002c9323cbd8a0689

# STEP 3: Generate SHA1 hash
echo -n "lokeshhhh" | sha1sum
# Output: fe51329c3faf684395899bdadbf7ab68fe673584

# STEP 4: Generate SHA256 hash
echo -n "lokeshhhh" | sha256sum

# STEP 5: Generate SHA512 hash
echo -n "password" | sha512sum

# STEP 6: Identify hash type using hashid
hashid 'a8ba8931efd321b002c9323cbd8a0689'
# Shows: MD5, MD4, Double MD5, etc.

hashid 'fe51329c3faf684395899bdadbf7ab68fe673584'
# Shows: SHA-1, Double SHA-1, etc.

# STEP 7: Identify hash from /etc/shadow
sudo cat /etc/shadow | head -3
# Identify the hash type shown

# ============================================================
# PART B: RAINBOW TABLE ATTACK
# ============================================================

# STEP 8: Find existing rainbow tables
find / -name "*.rt" 2>/dev/null
ls /usr/share/rainbowcrack/ 2>/dev/null

# STEP 9: Copy rainbow tables to working directory
cp /usr/share/rainbowcrack/*.rt . 2>/dev/null || echo "No pre-built tables found"

# STEP 10: Sort rainbow table (required before lookup)
rtsort /usr/share/rainbowcrack/md5_loweralpha#1-6_0_2400x200000_0.rt 2>/dev/null || \
rtsort md5_loweralpha#1-6_0_2400x200000_0.rt 2>/dev/null

# STEP 11: Use rcrack to crack a hash using rainbow table
rcrack /usr/share/rainbowcrack -h a8ba8931efd321b002c9323cbd8a0689 2>/dev/null || \
rcrack . -h a8ba8931efd321b002c9323cbd8a0689

# STEP 12: Online rainbow table lookup (demonstrate concept)
echo "Visit https://crackstation.net"
echo "Paste this MD5 hash: 5f4dcc3b5aa765d61d8327deb882cf99"
echo "It will reveal: password"

echo "Paste SHA1: fe51329c3faf684395899bdadbf7ab68fe673584"
echo "Not found = hash is for uncommon word (rainbow table miss)"

# ============================================================
# PART C: SECURE HASHING (bcrypt, Argon2) – DEFENSE
# ============================================================

# STEP 13: bcrypt demo via Python
python3 -c "
import bcrypt
password = b'mypassword'
salt = bcrypt.gensalt()
hashed = bcrypt.hashpw(password, salt)
print('BCrypt hash:', hashed.decode())
print('Verify correct:', bcrypt.checkpw(password, hashed))
print('Verify wrong:', bcrypt.checkpw(b'wrongpass', hashed))
"

# STEP 14: Argon2 demo via Python
python3 -c "
from argon2 import PasswordHasher
ph = PasswordHasher()
hash = ph.hash('mypassword')
print('Argon2 hash:', hash)
print('Verify:', ph.verify(hash, 'mypassword'))
" 2>/dev/null || echo "Install: pip install argon2-cffi --break-system-packages"

# STEP 15: Compare hash speeds (salted vs unsalted)
echo "MD5 (fast, no salt):"
time echo -n "password" | md5sum

echo "SHA256 (fast, no salt):"
time echo -n "password" | sha256sum

echo "BCrypt (slow, salted - SECURE):"
time python3 -c "import bcrypt; bcrypt.hashpw(b'password', bcrypt.gensalt(rounds=12))"

# ============================================================
# SUMMARY TABLE
# ============================================================
echo "
=== HASH COMPARISON TABLE ===
Algorithm | Speed  | Salt | Rainbow Resistant | Use For
----------|--------|------|-------------------|---------
MD5       | Fast   | No   | No                | Checksums only
SHA1      | Fast   | No   | No                | Legacy (broken)
SHA256    | Fast   | No   | No                | File integrity
BCrypt    | Slow   | Yes  | Yes               | Password storage
Argon2    | Slow   | Yes  | Yes               | Password storage (best)
"

# Conclusion:
# Hashing algorithms (MD5, SHA1, SHA256) were used to generate password hashes.
# hashid identified hash types from their format.
# Rainbow table attack demonstrated that unsalted MD5/SHA1 hashes are crackable.
# bcrypt and Argon2 resist rainbow table attacks due to salting and slow computation.
# Lesson: Always use bcrypt/Argon2 for password storage, never plain MD5/SHA1.
