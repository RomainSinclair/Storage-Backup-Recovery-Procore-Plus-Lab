# Storage Backup Recovery — Procore-Plus Lab

Storage, Backup & Recovery — NFS and FTP services, LVM storage extension, kernel module backups, and Bacula backup/restore operations.

## Environment
CentOS Stream / RHEL-based VMs in the Procore-Plus lab (dev-app, stage-web, dev-performance hosts), managed as a production-style environment with Jira ticket tracking.

## Skills & Tools
NFS server/client, vsftpd, LVM (lvextend, filesystem growth), tar archiving, Bacula (bacula-fd, bconsole restore), disaster recovery

## Tickets

| Ticket | Title | Documentation |
| --- | --- | --- |
| #49 | Configure FTP Server and Client (vsftpd) | [ticket-49-ftp-server-client.md](tickets/ticket-49-ftp-server-client.md) |
| #52 | Create Backups for Server Kernel Modules | [ticket-52-kernel-module-backups.md](tickets/ticket-52-kernel-module-backups.md) |
| #56 | Storage Request — Extend /lfjs/logs by 100 MB (LVM) | [ticket-56-lvm-storage-extension.md](tickets/ticket-56-lvm-storage-extension.md) |
| #63 | Configure an NFS Server | [ticket-63-nfs-server-configuration.md](tickets/ticket-63-nfs-server-configuration.md) |
| #64 | Install and Configure Bacula Backup Client | [ticket-64-bacula-client-install.md](tickets/ticket-64-bacula-client-install.md) |
| #66 | Restore Performance Script via Bacula | [ticket-66-bacula-restore.md](tickets/ticket-66-bacula-restore.md) |

## About
Each ticket document includes the objective, environment details, step-by-step resolution, commands used, and verification/outcome. These reflect real hands-on system administration work completed in a lab modeled on production operations.
