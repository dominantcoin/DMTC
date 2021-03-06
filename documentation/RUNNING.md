## Definition

This document describes a few way how to run Dmtc client persistently remotely(on VPS).

### Options

We will imply that user is connected via ssh to remote host.

1. Simply running `# /path/to/dmtcd`
2. Running as daemon `# /path/to/dmtcd -daemon` or placing daemon=1 into `dmtc.conf`
3. Running as service(systemd, init.d, etc)

Let's breakdown every solution. 

1. This is the worst solution since it runs as blocking process to the terminal and you are pretty much limited to do anything. 
2. This solution is better because it forks process and client runs in background as daemon, in this case you can safely close your connection and everything will work.
3. This solution is by far the best because it does the same as 2) but also supports auto-restart and auto-start. (Recommended)

### Running as service(systemd)

To run client as systemd daemon we need to make simple configuration. 

Unit file can be found at our github repository, `dmtc/contrib/init/dmtcd.service`

1. Place so `dmtcd.service` unit file into `/etc/systemd/system`. 
2. Create folder for datadir: `sudo mkdir -p /var/lib/dmtcd`
3. Enable unit for auto-start: `sudo systemctl start dmtcd`. (Optional)
4. Start service: `sudo systemctl start dmtcd`
5. Check status: `sudo systemctl status dmtcd`

Useful commands:

* `sudo systemctl status dmtcd` - shows status
* `sudo systemctl stop dmtcd` - stops daemon
* `tail -f /var/lib/dmtcd/debug.log` - show logs

#### Using our dmtcd.service

Our unit file that is provided at `dmtc/contrib/init/dmtcd.service` has configuration for different from default `dmtc.conf` and `datadir` folders. 
Default is: 

* `-datadir=/var/lib/dmtcd`
* `-conf=/etc/dmtc.conf`

Using our unit file won't place `datadir` and `dmtc.conf` under `~/.dmtc`. If you want to change those locations, you will need to modify unit file. 

### Daemon configuration

If you want fluid support of auto-start it might be useful to add `reindex=1` into `dmtc.conf`. Motivation for this is that when daemon crashes database might corrupt, `reindex=1` will slow things a lot on restarts but will provide automatic robust experience.

It makes sense to setup strong `rpcpassword`, `rpcuser` and `rpcallowip` in `dmtc.conf` to secure a bit this client.

If wallet contains any funds, it needs to be encrypted with strong passphrase. 