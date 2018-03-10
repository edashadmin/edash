Sample init scripts and service configuration for edashd
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/edashd.service:    systemd service unit configuration
    contrib/init/edashd.openrc:     OpenRC compatible SysV style init script
    contrib/init/edashd.openrcconf: OpenRC conf.d file
    contrib/init/edashd.conf:       Upstart service configuration file
    contrib/init/edashd.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three Linux startup configurations assume the existence of a "edashcore" user
and group.  They must be created before attempting to use these scripts.
The OS X configuration assumes edashd will be set up for the current user.

2. Configuration
---------------------------------

At a bare minimum, edashd requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, edashd will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that edashd and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If edashd is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running edashd without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see `contrib/debian/examples/edash.conf`.

3. Paths
---------------------------------

3a) Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              `/usr/bin/edashd`  
Configuration file:  `/etc/edashcore/edash.conf`  
Data directory:      `/var/lib/edashd`  
PID file:            `/var/run/edashd/edashd.pid` (OpenRC and Upstart) or `/var/lib/edashd/edashd.pid` (systemd)  
Lock file:           `/var/lock/subsys/edashd` (CentOS)  

The configuration file, PID directory (if applicable) and data directory
should all be owned by the edashcore user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
edashcore user and group.  Access to edash-cli and other edashd rpc clients
can then be controlled by group membership.

3b) Mac OS X

Binary:              `/usr/local/bin/edashd`  
Configuration file:  `~/Library/Application Support/EDashCore/edash.conf`  
Data directory:      `~/Library/Application Support/EDashCore`
Lock file:           `~/Library/Application Support/EDashCore/.lock`

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start edashd` and to enable for system startup run
`systemctl enable edashd`

4b) OpenRC

Rename edashd.openrc to edashd and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/edashd start` and configure it to run on startup with
`rc-update add edashd`

4c) Upstart (for Debian/Ubuntu based distributions)

Drop edashd.conf in /etc/init.  Test by running `service edashd start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

4d) CentOS

Copy edashd.init to /etc/init.d/edashd. Test by running `service edashd start`.

Using this script, you can adjust the path and flags to the edashd program by
setting the EDASHD and FLAGS environment variables in the file
/etc/sysconfig/edashd. You can also use the DAEMONOPTS environment variable here.

4e) Mac OS X

Copy org.edash.edashd.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.edash.edashd.plist`.

This Launch Agent will cause edashd to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run edashd as the current user.
You will need to modify org.edash.edashd.plist if you intend to use it as a
Launch Daemon with a dedicated edashcore user.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
