Procore Linux Jira Tickets  |  Prepared by: Romain Sinclair

| Ticket #64 **Install and Configure Bacula Backup Client** | **COMPLETED** |
| --- | --- |

| **Bacula Clients** | dev-app-rs1, stage-web-rs1, dev-performance-rs1 |
| --- | --- |
| **Service** | bacula-fd (File Daemon, port 9102/tcp) |
| **Install Version** | Bacula 9.6.6 (compiled from source) |
| **Config File** | /etc/bacula/bacula-fd.conf |
| **Category** | Backup & Recovery |

**Objective**

Build confidence in data protection by installing and configuring the Bacula file daemon on all three lab servers and performing backup jobs from the Bacula server. This required building Bacula from source tarball.

**Requirements**

- Install bacula-fd on: dev-app, stage-web, dev-performance

- Create a systemd service unit for bacula-fd

- Connect each client to the Bacula director

- Perform backup jobs from the Bacula server web interface

**Implementation Steps**

# Step 1: Install build dependencies
sudo dnf groupinstall "Development Tools" -y
sudo dnf install gcc gcc-c++ make cmake autoconf libtool openssl-devel \
  readline-devel ncurses-devel zlib-devel lzo lzo-devel sqlite-devel libacl-devel -y

# Step 2: Download and extract Bacula source
cd ~
curl -LO https://sourceforge.net/projects/bacula/files/bacula/9.6.6/bacula-9.6.6.tar.gz
tar -xvzf bacula-9.6.6.tar.gz
cd bacula-9.6.6

# Step 3: Configure and compile (client only)
./configure --enable-client-only
make -j$(nproc)
sudo make install

# Step 4: Create systemd service unit
sudo vi /etc/systemd/system/bacula-fd.service

# Step 5: Reload systemd and start the service
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl start bacula-fd
sudo systemctl enable --now bacula-fd

# Step 6: Verify the daemon is listening
sudo ss -tulnp | grep 9102

**Troubleshooting ****&**** Root Cause Analysis**

This was one of the most troubleshooting-intensive tickets:

- curl download initially failed because the working directory was not writable — resolved by switching to the home directory

- Bacula client repeatedly timed out from the director after install

- bacula-fd crashed under systemd — manual config validation revealed duplicate Director blocks both named "prod-bacula" in bacula-fd.conf

- Commenting out the second duplicate Director resource block resolved the startup crash

Key diagnostic command: sudo /usr/sbin/bacula-fd -t -c /etc/bacula/bacula-fd.conf — this runs a config test without starting the daemon.

**Validation ****&**** Testing**

# Validate config syntax first
sudo /usr/sbin/bacula-fd -t -c /etc/bacula/bacula-fd.conf

# Reset any failed state and restart
sudo systemctl reset-failed bacula-fd
sudo systemctl restart bacula-fd
sudo systemctl status bacula-fd --no-pager
sudo ss -tulnp | grep 9102

**Key Lessons Learned**

- Always run the daemon with -t (config test) before starting under systemd

- Duplicate named Director blocks in bacula-fd.conf cause a silent crash — check for duplicate resource names

- Build from source requires a writable working directory — use $HOME, not /tmp or other restricted paths


Procore-Plus Lab Environment  |  CentOS Stream / RHEL  |  
