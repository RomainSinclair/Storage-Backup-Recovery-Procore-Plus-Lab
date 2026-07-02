**Ticket #49 — Configure FTP Server and Client**

*Category: Web Server Administration / FTP*

| **Field** | Value |
| --- | --- |
| **Ticket #** | 49 |
| **Title** | Configure FTP Server and Client |
| **Category** | Web Server Administration / FTP |
| **Prepared by** | Romain Sinclair |
| **Environment** | Procore-Plus Lab (CentOS Stream / RHEL-based) |

# **Objective**

Configure vsftpd as an FTP server on dev-app, configure the FTP client on stage-web, and successfully download the file ftp-prod.config via FTP transfer.

# **Requirements**

- FTP server: dev-app

- FTP client: stage-web

- Successful FTP download of ftp-prod.config

- Use file from /nfs/incoming/vhosts/ftp-files/ftp-prod.config

# **Environment Details**

- FTP server: dev-app

- FTP client: stage-web

- File: ftp-prod.config

- Source path: /nfs/incoming/vhosts/ftp-files/ftp-prod.config

# **Implementation Steps**

Step 1 — On dev-app (server), install vsftpd and ftp client packages:

sudo dnf install -y vsftpd ftp

Step 2 — Enable and start vsftpd:

sudo systemctl enable --now vsftpd

Step 3 — Open the firewall for FTP:

sudo firewall-cmd --add-service=ftp --permanent

sudo firewall-cmd --reload

Step 4 — Copy the target file into the FTP root for download:

cp /nfs/incoming/vhosts/ftp-files/ftp-prod.config ~/ftp-prod.config

Step 5 — From stage-web (client), connect to the FTP server:

ftp <ftp_server_ip>

Step 6 — Download the file:

get ftp-prod.config

# **Troubleshooting ****&**** Root Cause Analysis**

Multiple issues arose during this ticket: commands were initially entered on the wrong server (FTP server config steps run on the client), incorrect files were created and needed to be removed, and there was confusion about whether to reinstall FTP packages.

The root cause of repeated transfer failures was mixing up the server and client roles. The vsftpd service must be on dev-app; the ftp get command must be run from stage-web.

Resolution: clean up incorrect files, confirm vsftpd is installed and running on dev-app, confirm ftp client is installed on stage-web, verify the file exists in the FTP user's home directory, then retry the transfer.

# **Validation ****&**** Testing**

systemctl status vsftpd

ls -l ~/ftp-prod.config

- Expected: vsftpd active and running, file downloaded to stage-web home directory

# **Key Lessons Learned**

- Always clarify which server is the FTP server and which is the client before starting

- vsftpd serves files from the user's home directory by default — files must be placed there for download

- firewall-cmd --add-service=ftp opens ports 20 and 21 together — don't open them individually

# **Screenshots**

***Figure 1: dev-app — vsftpd service status and FTP configuration***

***Figure 2: stage-web FTP client connecting to dev-app and file download***

Ticket #49 | Configure FTP Server and Client | Procore Linux Jira  |  Page  of