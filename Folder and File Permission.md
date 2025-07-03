
### Create a Group
```bash
sudo groupadd <group name>
```

### Check all users
```bash
getent group | cut -d: -f1
```

### Add users to the Group
```bash
sudo usermod -a -G developers <user>
```

### Check the Users present in the group
```bash
getent group <group>
```

### Remove user from group
```bash
sudo gpasswd -d <user> <group>
```

### Delete a Group
```bash
sudo groupdel <group>
```

---
## Shared Folder Management

### Create a shared folder for the group
```bash
sudo mkdir /path/to/shared-folder
sudo chown root:<group> /path/to/shared-folder
```

### Set permissions with setgid bit (files inherit group)
```bash
sudo chmod 2775 /path/to/shared-folder
```
_The `2` sets the setgid bit, `775` gives full access to owner and group_

### Apply permissions recursively to existing content
```bash
sudo chmod -R 2775 /path/to/shared-folder
sudo chgrp -R <group> /path/to/shared-folder
```

