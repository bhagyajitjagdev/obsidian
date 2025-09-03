This guide walks you through creating a new SSH user on your Linux server with configurable privileges.

## Prerequisites

- Windows machine with Git installed
- Existing access to your server as `ubuntu` user
- Server IP address

---

## Step 1: Create SSH Key Pair (Windows)

### 1.1 Navigate to your keys directory

```bash
# Open Git Bash or PowerShell
cd C:\Batch\keys\[username]
```

_Replace `[username]` with your desired username_

### 1.2 Generate SSH key pair

```bash
ssh-keygen -t rsa -b 4096 -f [username]_key -C "[username]"
```

- Press Enter for no passphrase (or set one if desired)
- This creates two files:
    - `[username]_key` (private key - keep secure)
    - `[username]_key.pub` (public key - to be copied to server)

### 1.3 Display public key for copying

```bash
cat [username]_key.pub
```

**Copy this entire output** - you'll need it in the next step.

---

## Step 2: SSH into Server and Create User

### 2.1 Connect to server as ubuntu

```bash
ssh ubuntu@[SERVER_IP] -i [path-to-ubuntu-key]
```

### 2.2 Create new user

```bash
# Create user with home directory and bash shell
sudo useradd -m -s /bin/bash [username]

# Verify user was created
sudo ls -la /home/
```

---

## Step 3: Configure SSH Access for New User

### 3.1 Create SSH directory structure

```bash
# Create .ssh directory
sudo mkdir -p /home/[username]/.ssh

# Create authorized_keys file
sudo nano /home/[username]/.ssh/authorized_keys
```

### 3.2 Add public key

- Paste the public key you copied from Step 1.3
- Save and exit (Ctrl+X, then Y, then Enter)

### 3.3 Set correct permissions and ownership

```bash
# Set ownership
sudo chown -R [username]:[username] /home/[username]/.ssh

# Set directory permissions
sudo chmod 700 /home/[username]/.ssh

# Set file permissions
sudo chmod 600 /home/[username]/.ssh/authorized_keys
```

---

## Step 4: Test SSH Connection (Regular User - No Sudo)

### 4.1 Test connection from Windows

```bash
ssh [username]@[SERVER_IP] -i C:\Batch\keys\[username]\[username]_key
```

### 4.2 Verify user limitations

The user should now be able to:

- ✅ Access their home directory
- ✅ Run basic commands (`ls`, `cd`, `cat`, etc.)
- ✅ Create/modify files in their home directory

The user should NOT be able to:

- ❌ Run `sudo` commands
- ❌ Access system directories
- ❌ Install packages
- ❌ Modify system configuration

---

## Step 5: Grant Sudo Privileges (Optional)

⚠️ **Warning**: Only proceed if you want to give the user administrative privileges.

### 5.1 Add user to sudo group

```bash
sudo usermod -aG sudo [username]
```

### 5.2 Test sudo access

SSH as the new user and try:

```bash
sudo ls
# This will ask for the user's password (which they don't have yet)
```

### 5.3 Set password for user (if using password-based sudo)

```bash
sudo passwd [username]
# Enter new password when prompted
```

---

## Step 6: Configure Passwordless Sudo (Optional)

⚠️ **Warning**: This gives the user full root access without password verification.

### 6.1 Check existing sudo configuration

```bash
sudo ls -la /etc/sudoers.d/
```

### 6.2 Add passwordless sudo configuration

```bash
# Create new sudoers file for the user
sudo nano /etc/sudoers.d/[username]
```

Add this content:

```
[username] ALL=(ALL) NOPASSWD:ALL
```

Save and exit.

### 6.3 Test passwordless sudo

```bash
# SSH as the new user
ssh [username]@[SERVER_IP] -i [path-to-key]

# Test sudo without password
sudo su
sudo apt update
```

---

## Quick Reference Commands

### Connect to server

```bash
# As ubuntu user
ssh ubuntu@[SERVER_IP] -i [ubuntu-key-path]

# As new user
ssh [username]@[SERVER_IP] -i C:\Batch\keys\[username]\[username]_key
```

### User privilege levels

|Configuration|Can SSH|Can sudo|Needs password for sudo|
|---|---|---|---|
|Basic user|✅|❌|N/A|
|Sudo user|✅|✅|✅|
|Passwordless sudo|✅|✅|❌|

---

## Security Best Practices

1. **Keep private keys secure** - Never share `[username]_key` files
2. **Use strong passphrases** for SSH keys if storing on shared machines
3. **Regular user principle** - Only grant sudo when necessary
4. **Monitor access** - Check `/var/log/auth.log` for SSH activity
5. **Key rotation** - Periodically regenerate SSH keys

---

## Troubleshooting

### Permission denied (publickey)

- Verify you're using the correct username and key file
- Check file permissions on server (700 for .ssh, 600 for authorized_keys)
- Ensure public key was copied correctly

### Sudo password prompts

- User needs either a password set (`sudo passwd [username]`) or passwordless sudo configured
- Check `/etc/sudoers.d/` for existing configurations

### User can't access home directory

- Check ownership: `sudo chown -R [username]:[username] /home/[username]`

---

## File Structure Summary

```
Windows:
C:\Batch\keys\<server>\[username]\
├── [username]_key      (private key - keep secure)
└── [username]_key.pub  (public key)

Server:
/home/[username]/
└── .ssh/
    └── authorized_keys (contains public key)

/etc/sudoers.d/
└── [username]          (passwordless sudo config)
```