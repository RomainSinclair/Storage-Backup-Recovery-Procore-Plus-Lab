Procore Linux Jira Tickets  |  Prepared by: Romain Sinclair

| Ticket #66 **Issues with the Performance Script — Restore via Bacula** | **COMPLETED** |
| --- | --- |

| **VMs** | dev-app-rs1, dev-performance-rs1, stage-web-rs1 |
| --- | --- |
| **Services** | httpd (Apache), nfs-server |
| **Restore Tool** | Bacula (bconsole restore) |
| **Restore Target** | /tmp/restore (then moved to original paths) |
| **Category** | Backup & Recovery |

**Objective**

Build confidence in data protection by restoring files deleted by the performance.sh script using Bacula, then verifying httpd and nfs-server are back online. This is the direct follow-on to Ticket #65 where performance.sh was run.

**Requirements**

- Identify which files/services were broken by performance.sh on dev-app, dev-performance, and stage-web

- Restore deleted/corrupted NFS and HTTPD files from Bacula backups

- Verify httpd and nfs-server are running correctly after restore

**Implementation Steps**

# Step 1: Assess damage — check service states
systemctl status httpd
systemctl status nfs-server
ls -l /etc/httpd/
ls -l /etc/exports

# Step 2: Open Bacula console on the Bacula server
bconsole

# Step 3: Initiate restore interactively
restore
# Select: client = dev-app-rs1.procore.prod1 (or stage-web / dev-performance)
# Select: most recent backup job
# Select: files to restore (httpd config, /etc/exports)
# Set: restore destination = /tmp/restore
run

# Step 4: Move restored files back to original paths
cp /tmp/restore/etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf
cp /tmp/restore/etc/exports /etc/exports

# Step 5: Restart and verify services
systemctl restart httpd
systemctl restart nfs-server
systemctl status httpd
systemctl status nfs-server

**Troubleshooting ****&**** Root Cause Analysis**

The performance.sh script caused collateral damage by deleting files critical to Apache and NFS. Key issues during the restore process:

- Selecting the correct Bacula job and client combination — important to pick the right backup job that predates the deletion

- Confirming the restore destination (/tmp/restore) then manually moving files to their original paths

- After restore, journalctl was needed to confirm there were no lingering config errors

**Validation ****&**** Testing**

systemctl status httpd
systemctl status nfs-server
curl -I http://localhost
exportfs -v
journalctl -u httpd --no-pager
journalctl -u nfs-server --no-pager


Procore-Plus Lab Environment  |  CentOS Stream / RHEL  |  
