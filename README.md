# Slurm Backfill Monitor

The Slurm backfill monitor is designed to monitor the health of the backfill queue by evaluating the slurm logs over time to determine if the backfill is occuring as expected.

## Getting Started

To use the backfill monitor, an instance of slurm must be present. The software should also be run by a non-root user (ideally a user specifically generated for this purpose) who can have access to files in the /etc/slurm and /var/log/slurm folder. The user must also be able to kill the slurmctld process.

### Prerequisites

Slurm must be running
A special user account for the program to run under (i.e. "nodemgr")
```
useradd nodemgr
```

Next, give the user permission to restart the slurmctl daemon. TO do this, edit /etc/sudoers as root and add the following

```
Cmnd_Alias MGRCMDS = /bin/kill, /bin/sed
nodemgr ALL = NOPASSWD: MGRCMDS
Defaults!MGRCMDS !requiretty
```

Now, give the same user access to the required files:

```
setfacl -m u:nodemgr:rwx /var/log/slurm/slurmctld.log
setfacl -m u:nodemgr:rwx /etc/slurm/slurm.conf
```
### Installing

Installation of the program is as simple as copying the backfillmon.sh script to a common location
```
git clone https://github.com/nauhpc/backfill-monitor
cp backfill-monitor/backfillmon.sh /common/adm/bin/
```

Next, implememnt the cronjob for the user by copying the content of the crontab file into the user's crontab
```
crontab -e
```
And save

Now you're all set! the crontab will trigger the backfill monitor script, which will in turn temporarily enable the backfill monitoring feature of slurm, and after completion will scan the logs for a completion of th backfill. It will then send an email if there's any issue.

## Authors

* **Christoph Schild** - [The Germanator](https://github.com/the-germanator)
