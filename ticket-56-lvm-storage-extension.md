Procore Linux Jira Tickets  |  Prepared by: Romain Sinclair

| Ticket #56 **Storage Request — Add 100 MB to /lfjs/logs** | **COMPLETED** |
| --- | --- |

| **VM / Host** | dev-app-rs1.procore.prod1 |
| --- | --- |
| **Filesystem** | /lfjs/logs |
| **Storage Added** | +100 MB |
| **Stack** | LVM + filesystem extension (ext4 or XFS) |
| **Category** | Storage Administration / LVM |

**Objective**

Gain experience in managing Linux storage by extending the /lfjs/logs filesystem by 100 MB on dev-app using LVM. This required identifying the backing logical volume, extending it, and growing the filesystem without unmounting.

**Requirements**

- VM: dev-app

- Filesystem: /lfjs/logs

- Add exactly +100 MB

**Implementation Steps**

# Step 1: Identify the current disk/LV layout
lsblk
df -h /lfjs/logs

# Step 2: Identify the logical volume path
lvdisplay | grep -A5 "lfjs"

# Step 3: Extend the LV by 100 MB
sudo lvextend -L +100M <lv_path>
# Example: lvextend -L +100M /dev/vg_data/lv_lfjs_logs

# Step 4: Grow the filesystem (choose based on fs type)
# For ext4:
sudo resize2fs <lv_path>

# For XFS:
sudo xfs_growfs /lfjs/logs

# Step 5: Verify new size
df -h /lfjs/logs

**Troubleshooting ****&**** Root Cause Analysis**

The key prerequisite was identifying the filesystem type before choosing the grow command:

- ext4: use resize2fs <device> — can be run on a live mounted filesystem

- XFS: use xfs_growfs <mountpoint> — requires the filesystem to be mounted

- Always run df -h before and after to confirm the size changed

- LVM must have free space in the volume group (vgdisplay) before lvextend will succeed

**Validation ****&**** Testing**

df -h /lfjs/logs
# Expect: ~100 MB larger than before
lsblk

**Screenshots**

*Jira ticket #56 requirements — 100 MB LVM ext4, mount path /lfjs/logs, owner/group webmasters*

*dev-app: LVM extension commands, firewall rules, and /lfjs/logs directory creation*

Procore-Plus Lab Environment  |  CentOS Stream / RHEL  |  Page