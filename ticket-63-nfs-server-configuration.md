**Ticket #63 — Configure an NFS Server**

*Category: Infrastructure / Network File Sharing (NFS)*

| **Field** | Value |
| --- | --- |
| **Ticket #** | 63 |
| **Title** | Configure an NFS Server |
| **Category** | Infrastructure / Network File Sharing (NFS) |
| **Prepared by** | Romain Sinclair |
| **Environment** | Procore-Plus Lab (CentOS Stream / RHEL-based) |

# **Objective**

Configure dev-app as an NFS server, create a share /nfs-rsinclair, place the required Java archive in the share, and mount/test it from stage-web. Configure a persistent mount using /etc/fstab.

# **Requirements**

- NFS server: dev-app

- NFS client: stage-web

- Share name: /nfs-rsinclair

- Successful mount and file transfer test

- Persistent mount configured in /etc/fstab

# **Environment Details**

- NFS server: dev-app (IP: 10.1.30.106)

- NFS client: stage-web

- Share: /nfs-rsinclair

- Client mount point: /mnt/nfs-rsinclair

# **Implementation Steps**

Step 1 — Install NFS utilities on server:

sudo dnf install -y nfs-utils

Step 2 — Enable and start the NFS server:

sudo systemctl enable --now nfs-server

Step 3 — Create the share directory:

sudo mkdir -p /nfs-rsinclair

sudo chmod 755 /nfs-rsinclair

Step 4 — Download the required archive into the share:

cd /nfs-rsinclair

sudo wget https://download.java.net/java/GA/jdk18.0.1.1/.../openjdk-18.0.1.1_linux-x64_bin.tar.gz

Step 5 — Export the share in /etc/exports:

sudo vi /etc/exports

Add: /nfs-rsinclair 10.1.0.0/16(rw,sync,no_root_squash)

Step 6 — Apply the export:

sudo exportfs -rav

exportfs -v

Step 7 — Open firewall for NFS:

sudo firewall-cmd --permanent --add-service=nfs

sudo firewall-cmd --reload

Step 8 — On stage-web (client), create mount point and mount:

sudo mkdir -p /mnt/nfs-rsinclair

sudo mount 10.1.30.106:/nfs-rsinclair /mnt/nfs-rsinclair

ls -lh /mnt/nfs-rsinclair

Step 9 — Make the mount persistent in /etc/fstab:

sudo vi /etc/fstab

Add: 10.1.30.106:/nfs-rsinclair  /mnt/nfs-rsinclair  nfs  defaults  0 0

sudo mount -a

# **Troubleshooting ****&**** Root Cause Analysis**

Several issues surfaced: export path mismatch between what was in /etc/exports and the actual directory name — the path must match exactly.

Mount failures occurred due to hostname resolution — using the server IP (10.1.30.106) instead of the hostname resolved this.

An NFSv4 lease renewal 'Permission denied' loop on the client indicated stale mount or export permission issues. Resolution: unmount the stale mount, clean up /etc/fstab if incorrect entries existed, re-export with exportfs -rav, and remount using the correct IP.

# **Validation ****&**** Testing**

exportfs -v

showmount -e <server_ip>

mount | grep nfs

ls -lh /mnt/nfs-rsinclair

# **Key Lessons Learned**

- Export path in /etc/exports must exactly match the created directory name — case-sensitive

- Use server IP instead of hostname in mount commands to avoid DNS resolution failures

- NFSv4 permission denied loops often indicate stale mounts — unmount and remount fresh


Ticket #63 | Configure an NFS Server | Procore Linux Jira  |  Page  of
