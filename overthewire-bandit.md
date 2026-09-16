# OverTheWire Bandit — Complete Write-Up

**Author:** vinay / https://github.com/vinaysec-ui
**Date Completed:** September 16, 2024
**Platform:** [OverTheWire](https://overthewire.org/wargames/bandit/)
**Levels:** 0 → 34

---

## About This Write-Up
This is my write-up for all 34 levels of OverTheWire Bandit.
I completed this as part of my cybersecurity learning journey
during my 2nd year of college.

---

## Level 0 → 1
**Concept:** SSH basics
**Command:** `ssh bandit0@bandit.labs.overthewire.org -p 2220`
**Solution:** Password was in the challenge description.

---

## Level 1 → 2
**Concept:** Files with special characters in names
**Command:** `cat ./-`
**Key Learning:** Use `./` before filenames that start with `-`
to prevent the shell from interpreting them as flags.

---

## Level 2 → 3
**Concept:** Files with spaces in names
**Command:** `cat "spaces in this filename"`
**Key Learning:** Wrap filenames with spaces in quotes.

---

## Level 3 → 4
**Concept:** Hidden files
**Command:** `ls -la` → `cat .hidden`
**Key Learning:** Files starting with `.` are hidden in Linux.

---

## Level 4 → 5
**Concept:** File type identification
**Command:** `file *` → `cat ./-file07`
**Key Learning:** Use `file` command to identify file types
when extensions are misleading.

---

## Level 5 → 6
**Concept:** Finding files by properties
**Command:** `find . -type f -size 1033c ! -executable`
**Key Learning:** `find` is incredibly powerful for locating
specific files based on size, type, and permissions.

---

## Level 6 → 7
**Concept:** System-wide file search
**Command:** `find / -user bandit7 -group bandit6 -size 33c 2>/dev/null`
**Key Learning:** Redirect stderr to /dev/null to suppress
permission denied errors.

---

## Level 7 → 8
**Concept:** grep for specific patterns
**Command:** `grep "millionth" data.txt`
**Key Learning:** grep is the fastest way to search for
text in large files.

---

## Level 8 → 9
**Concept:** Finding unique lines
**Command:** `sort data.txt | uniq -u`
**Key Learning:** `uniq -u` shows only lines that appear
exactly once. Must sort first.

---

## Level 9 → 10
**Concept:** Extracting text from binary data
**Command:** `strings data.txt | grep "=="`
**Key Learning:** `strings` extracts readable text from
binary files. Combined with grep, it's very powerful.

---

## Level 10 → 11
**Concept:** Base64 decoding
**Command:** `base64 -d data.txt`
**Key Learning:** Base64 is a common encoding in CTFs
and real-world obfuscation.

---

## Level 11 → 12
**Concept:** ROT13 cipher
**Command:** `cat data.txt | tr 'a-zA-Z' 'n-za-mN-ZA-M'`
**Key Learning:** `tr` can perform character substitution.
ROT13 shifts each letter by 13 positions.

---

## Level 12 → 13
**Concept:** Multi-layer decompression
**Commands:**
xxd -r data.txt > data.bin
file data.bin # gzip
mv data.bin data.gz && gunzip data.gz
file data # bzip2
mv data data.bz2 && bunzip2 data.bz2

... repeat for tar, gzip, etc.

**Key Learning:** Files can be compressed multiple times
in different formats. Use `file` at each step to identify
the next layer.

---

## Level 13 → 14
**Concept:** SSH key authentication
**Command:** `ssh -i sshkey.private bandit14@localhost`
**Key Learning:** SSH keys replace passwords. The private
key file must have 600 permissions.

---

## Level 14 → 15
**Concept:** Netcat for port communication
**Command:** `nc localhost 30000` → paste password
**Key Learning:** Netcat can read/write to network ports.

---

## Level 15 → 16
**Concept:** SSL/TLS connections
**Command:** `openssl s_client -connect localhost:30001`
**Key Learning:** `openssl s_client` is like netcat but
for encrypted connections.

---

## Level 16 → 17
**Concept:** Port scanning + SSL
**Commands:**
nmap -sV -p 31000-32000 localhost
openssl s_client -connect localhost:31790

text

**Key Learning:** Always scan port ranges to find the
correct service. Not all open ports speak SSL.

---

## Level 17 → 18
**Concept:** Comparing files
**Command:** `diff passwords.old passwords.new`
**Key Learning:** `diff` shows exactly what changed
between two files.

---

## Level 18 → 19
**Concept:** Bypassing auto-logout
**Command:** `ssh bandit18@... -p 2220 "cat readme"`
**Key Learning:** You can execute commands over SSH
without getting an interactive shell.

---

## Level 19 → 20
**Concept:** SUID binaries
**Command:** `./bandit20-do cat /etc/bandit_pass/bandit20`
**Key Learning:** SUID binaries run with the file owner's
permissions, not yours.

---

## Level 20 → 21
**Concept:** Netcat listener + reverse connection
**Commands:**
Terminal 1:
nc -lvp 4444

Terminal 2:
./suconnect 4444

**Key Learning:** Understanding listeners and connections
is fundamental to reverse shells.

---

## Level 21 → 22
**Concept:** Cron jobs (basic)
**Commands:**
cat /etc/cron.d/cronjob_bandit22
cat /usr/bin/cronjob_bandit22.sh
cat /tmp/<filename_from_script>

**Key Learning:** Cron jobs run on schedules. If they
write to world-readable files, anyone can read the output.

---

## Level 22 → 23
**Concept:** Cron jobs with dynamic filenames
**Commands:**
cat /usr/bin/cronjob_bandit23.sh
echo "I am user bandit23" | md5sum | cut -d ' ' -f 1
cat /tmp/<md5_hash>

**Key Learning:** Scripts using `whoami` behave differently
depending on the executing user.

---

## Level 23 → 24
**Concept:** Cron job script injection
**Commands:**
echo '#!/bin/bash' > /tmp/myscript.sh
echo 'cat /etc/bandit_pass/bandit24 > /tmp/mypass' >> /tmp/myscript.sh
chmod +x /tmp/myscript.sh
cp /tmp/myscript.sh /var/spool/bandit24/

Wait 60 seconds
cat /tmp/mypass

**Key Learning:** If a cron job executes files from a
writable directory, you can inject your own script to
run as that user. THIS IS A REAL-WORLD ATTACK.

---

## Level 24 → 25
**Concept:** Brute-forcing a service
**Commands:**
Write a script to try all 4-digit PINs
for i in {0000..9999}; do
echo "UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ $i" | nc localhost 30002
done | grep -v "Wrong"

**Key Learning:** Brute-forcing is slow but effective
against short PINs/passwords.

---

## Level 25 → 26 ⭐ (Hardest Level)
**Concept:** Restricted shell escape via vim
**Steps:**
1. Shrink terminal window
2. `ssh -i bandit26.sshkey bandit26@localhost`
3. When `--More--` appears, press `v` to open vim
4. In vim: `:set shell=/bin/bash` then `:shell`
**Key Learning:** Restricted shells can be escaped through
programs that allow command execution (vim, less, more,
awk, python). Check GTFOBins for real-world escapes.

---

## Level 26 → 27
**Concept:** SUID binary for command execution
**Command:** `./bandit27-do cat /etc/bandit_pass/bandit27`

---

## Level 27 → 28
**Concept:** Git clone basics
**Command:** `git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo`

---

## Level 28 → 29
**Concept:** Git history mining
**Command:** `git log -p` → find password in old commit
**Key Learning:** Deleted data lives forever in git history.

---

## Level 29 → 30
**Concept:** Git branch enumeration
**Command:** `git branch -a` → `git checkout dev`
**Key Learning:** Sensitive data hides in non-main branches.

---

## Level 30 → 31
**Concept:** Git tags
**Command:** `git tag` → `git show secret`
**Key Learning:** Always check tags during git recon.

---

## Level 31 → 32
**Concept:** Git push + .gitignore bypass
**Command:** `git add -f key.txt` → `git push`
**Key Learning:** `.gitignore` can be overridden with `-f`.

---

## Level 32 → 33
**Concept:** Uppercase shell escape
**Command:** Type `$0` to drop into normal bash
**Key Learning:** Environment variables bypass character filters.

---

## Level 33 → 34
**Concept:** Final level
**Command:** `cat /etc/bandit_pass/bandit34`

---

## Key Takeaways
1. **Linux command line is the foundation** of everything in cybersecurity
2. **Cron jobs and SUID** are the most common privilege escalation vectors
3. **Git is a goldmine** for leaked secrets
4. **Restricted shells can almost always be escaped**
5. **Always enumerate thoroughly** before exploiting

## Tools Used
- SSH, Netcat, OpenSSL, Nmap
- grep, find, sort, uniq, strings, xxd
- git, diff, tr, base64
- vim (for shell escape!)

## What's Next
Moving on to web application pentesting:
SQL Injection, XSS, Burp Suite, and real CTF machines.

---
*This write-up is part of my cybersecurity learning journey.
Follow my progress on GitHub: https://github.com/vinaysec-ui
