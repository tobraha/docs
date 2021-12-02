# Chroot Jail Configuration

How I configured a secure chroot jail for sftp only.

### sftp chroot jail for Posix systems

1. Create user(s), group, and root sftp folder

```bash
mkdir /sftp
groupadd sftpusers
useradd -d /sftp/mysftp -M -N -g sftpusers mysftp
```

  - '-M' and '-N' are used to: not create home directory, and do not create a group with the same name (respectively)

2. Permissions for those folders

  - The actual user folder must be owned by root; you cannot write new files here as the chroot user
  - New files must be written to a sub-directory within that is owned by the sftp user

```bash
ls -l /sftp
drwxr-xr-x  3 root root  4096 Jan  1  2021 mysftp

ls -l /sftp/mysftp
drwxr-xr-x 2 mysftp sftpusers 4096 Jan  1  2021 upload

mkdir /sftp/mysftp/upload
chown mysftp:sftpusers /sftp/mysftp/upload
```

3. OpenSSH Settings

```bash
# override default of no subsystems
Subsystem sftp  /usr/lib/openssh/sftp-server

Match User mysftp
      PasswordAuthentication yes
      ChrootDirectory %h
      ForceCommand internal-sftp
      AllowTcpForwarding no
      X11Forwarding no
```
