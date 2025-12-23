# Code Challenges: Linux

Shell scripting challenges to practice Linux command-line skills. Complete each challenge in a separate file.

---

## Challenge 1: File Organizer
**File:** `file-organizer.sh`

Write a script that organizes files in a directory by their extension.

```bash
./file-organizer.sh /path/to/messy-folder
```

**Expected behavior:**
- Creates subdirectories for each file type (e.g., `txt/`, `pdf/`, `jpg/`)
- Moves files into their respective directories
- Prints summary of files moved

**Example:**
```
Organized 5 .txt files
Organized 3 .pdf files
Organized 2 .jpg files
```

---

## Challenge 2: Process Monitor
**File:** `process-monitor.sh`

Write a script that monitors a process and alerts when it stops.

```bash
./process-monitor.sh nginx 5
# Checks every 5 seconds if nginx is running
```

**Requirements:**
- Accept process name and check interval as arguments
- Print timestamp with each check
- Exit with message when process stops
- Handle case when process doesn't exist

---

## Challenge 3: Disk Space Alert
**File:** `disk-alert.sh`

Write a script that checks disk usage and warns if above threshold.

```bash
./disk-alert.sh 80
# Alerts if any partition is above 80% usage
```

**Expected output:**
```
WARNING: /dev/sda1 is at 85% (threshold: 80%)
OK: /dev/sda2 is at 45%
```

**Requirements:**
- Parse `df` output
- Accept threshold as argument (default: 90)
- Exit code 1 if any partition exceeds threshold

---

## Challenge 4: Backup Script
**File:** `backup.sh`

Write a script that creates timestamped backups of a directory.

```bash
./backup.sh /path/to/source /path/to/backup-dest
```

**Requirements:**
- Create compressed tar archive with timestamp
- Format: `backup-YYYYMMDD-HHMMSS.tar.gz`
- Keep only last 5 backups (delete older ones)
- Print backup size when complete

---

## Challenge 5: User Audit
**File:** `user-audit.sh`

Write a script that audits user accounts on the system.

```bash
./user-audit.sh
```

**Expected output:**
```
=== User Audit Report ===
Total users: 25
Users with shell access: 5
Users without password: 2
  - testuser
  - guest
Last login info for shell users:
  - root: 2025-01-15
  - admin: 2025-01-14
```

---

## Submission
Place all challenge files in: `01-linux/challenges/`

| Challenge | File | Status |
|-----------|------|--------|
| File Organizer | `file-organizer.sh` | ⬜ |
| Process Monitor | `process-monitor.sh` | ⬜ |
| Disk Space Alert | `disk-alert.sh` | ⬜ |
| Backup Script | `backup.sh` | ⬜ |
| User Audit | `user-audit.sh` | ⬜ |
