
Debian
====================
This directory contains files used to package edashd/edash-qt
for Debian-based Linux systems. If you compile edashd/edash-qt yourself, there are some useful files here.

## edash: URI support ##


edash-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install edash-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your edash-qt binary to `/usr/bin`
and the `../../share/pixmaps/edash128.png` to `/usr/share/pixmaps`

edash-qt.protocol (KDE)

