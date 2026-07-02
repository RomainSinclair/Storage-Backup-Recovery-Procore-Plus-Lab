**Ticket #52 — Create Backups for Server Kernel**

*Category: Terminal / CLI / Scripting — Backup*

| **Field** | Value |
| --- | --- |
| **Ticket #** | 52 |
| **Title** | Create Backups for Server Kernel |
| **Category** | Terminal / CLI / Scripting — Backup |
| **Prepared by** | Romain Sinclair |
| **Environment** | Procore-Plus Lab (CentOS Stream / RHEL-based) |

# **Objective**

Back up /lib/modules/ on both dev-app and stage-web into /nfs/incoming/vhosts/backup, naming each archive with the server name and date for identification.

# **Requirements**

- VMs: dev-app, stage-web

- Source path: /lib/modules/

- Backup location: /nfs/incoming/vhosts/backup

- Name backups with server names

# **Environment Details**

- VMs: dev-app, stage-web

- Source: /lib/modules/

- Backup path: /nfs/incoming/vhosts/backup

# **Implementation Steps**

Step 1 — On dev-app, create the backup archive:

tar -czf /nfs/incoming/vhosts/backup/dev-app-$(date +%F).tar.gz /lib/modules/

Step 2 — On stage-web, create the backup archive:

tar -czf /nfs/incoming/vhosts/backup/stage-web-$(date +%F).tar.gz /lib/modules/

Step 3 — Verify both archives were created:

ls -lh /nfs/incoming/vhosts/backup

Step 4 — Spot-check one archive for integrity:

tar -tzf /nfs/incoming/vhosts/backup/dev-app-<date>.tar.gz | head

# **Troubleshooting ****&**** Root Cause Analysis**

The core work involved ensuring the NFS backup path (/nfs/incoming/vhosts/backup) was writable from both VMs and that archive files were named appropriately with server name + date.

If the NFS path was not mounted or not writable, the tar command would fail silently or produce a permission denied error. Verify mount status with 'mount | grep nfs' before running backups.

# **Validation ****&**** Testing**

ls -lh /nfs/incoming/vhosts/backup

tar -tzf /nfs/incoming/vhosts/backup/<backup_name>.tar.gz | head

# **Key Lessons Learned**

- Use $(date +%F) in the filename to embed the ISO date (YYYY-MM-DD) for easy identification

- tar -tzf lists archive contents without extracting — use for integrity checks

- Verify the NFS backup destination is mounted and writable before running backup jobs


Ticket #52 | Create Backups for Server Kernel | Procore Linux Jira  |  Page  of
