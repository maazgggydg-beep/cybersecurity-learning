# 🐧 Linux Command Cheatsheet

## File & Directory Operations

```bash
# List files
ls -la              # Detailed listing with hidden files
ls -lh              # Human-readable file sizes

# Navigation
pwd                 # Print working directory
cd /path/to/dir     # Change directory
cd ~                # Home directory
cd ..               # Parent directory

# File operations
cp source dest      # Copy files
mv source dest      # Move/rename files
rm filename         # Delete files
rm -r directory     # Delete directory recursively
mkdir dirname       # Create directory
touch filename      # Create empty file

# View files
cat filename        # Display file content
less filename       # View with pagination
head -n 10 file     # First 10 lines
tail -n 10 file     # Last 10 lines
```

## File Permissions

```bash
# Change permissions
chmod 755 file      # rwxr-xr-x
chmod 644 file      # rw-r--r--
chmod +x file       # Add execute permission
chmod -x file       # Remove execute permission

# Change owner
chown user:group file

# Permission meanings
# 7 = rwx (read, write, execute)
# 6 = rw- (read, write)
# 5 = r-x (read, execute)
# 4 = r-- (read only)
```

## Search & Find

```bash
find /path -name "*.txt"        # Find files by name
find /path -type f -size +1M    # Find large files
grep "pattern" file             # Search text in file
grep -r "pattern" .             # Recursive search
grep -i "pattern" file          # Case-insensitive
```

## User & Permissions

```bash
whoami              # Current user
id                  # User ID and groups
sudo command        # Run as admin
su - username       # Switch user
```

## Process Management

```bash
ps aux              # List all processes
ps aux | grep name  # Find specific process
kill PID            # Kill process by ID
kill -9 PID         # Force kill
top                 # Real-time processes
background          # Run process in background
fg                  # Bring to foreground
```

## Network Commands

```bash
ifconfig            # Network interfaces
ip addr show        # IP addresses
netstat -tuln       # Listen ports
ss -tuln            # Socket statistics
ping host           # Test connectivity
nslookup domain     # DNS lookup
curl url            # Download/test URLs
wget url            # Download files
ssh user@host       # SSH connection
```

## File Transfer

```bash
scp file user@host:/path    # Secure copy
rsync -av src dest          # Sync files
ftp host                    # FTP connection
```

## System Info

```bash
uname -a            # System information
cat /etc/os-release # OS details
lsb_release -a      # Ubuntu info
df -h               # Disk usage
du -sh /path        # Directory size
free -h             # Memory usage
```

## Archiving

```bash
tar -czf file.tar.gz dir     # Compress
tar -xzf file.tar.gz         # Extract
zip -r file.zip dir          # Zip archive
unzip file.zip               # Unzip
```

## Useful Shortcuts

```bash
Ctrl+C              # Cancel command
Ctrl+Z              # Suspend process
Ctrl+D              # EOF/Exit
Ctrl+A              # Move to line start
Ctrl+E              # Move to line end
Ctrl+R              # Search history
!command            # Repeat command
!!                  # Repeat last command
```

---
*Bookmark this for quick reference!*