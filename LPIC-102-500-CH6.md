# 6. Configuring the gui, loaclization, printer

## UNDERSTANDING THE GUI

GUI components:

Desktop environment <-> Windows manager <-> Display server

Each Desktop environment has its own default window manager.

Display server: Program that uses a comunication protocol to transmit the desires of the UI to the OS. Communication protocol (display server protocol) can operate over a network

Compositor: A member in the display server that arranges various display elements within a window to create a screen image to be passed back to the client.

## UNDERSTANDING THE X11 ARCHITECTURE

### Examining X.Org

X.Org package keeps track of display card, monitor, and input device information in a config file.

Primary configuration file: /etc/X11/xorg.conf

If xorg.conf in /etc, tipically empty.

Individual applications or devices config: /etc/X11/xorg.conf.d

X.Org software can create a session config on the fly using runtime auto-detection of the hardware. That's why xorg.conf or xorg.conf.d may not be available.

We can manually create the config file:

* Stop the server with `sudo telinit 3`
* Generate the config file xorg.config.new in the working dir: `sudoo Xorg -configure`
* After tweaking move to correct location
* Restart X server

xorg.conf sections:

* Input Device
* Monitor
* Modes
* Device
* Screen
* Module
* Files
* Server Flags
* Server Layout

For troubleshooting we can check `~/.xsession-errors`.

We can also use xdpyinfo and xwininfo.

xdpyinfo: Provides info about the X.Org server.
xwininfo: Provides windows information. With no options an interactive utility asks you to click on the window for which youd desire statistics like windwow's dimensions.

### Figuring Out Wayland

Check display server:

* `echo WAYLAND_DISPLAY`
* `loginctl`: this will show the session number
* `loginctl show-session session-number -p Type`: change session-number with the session number. Type=wayland or Type=X11 should be the result

Wayland is a replacement for the X.Org display server, but it's also a term that covers the compositor, the window server and the display server.

Wayland compositor is Weston. Weston provides a basic desktop experience because it was created as a Wayland compositor reference implementation.

Wayland's compositor is swappable for other compositors like Arcan, Sway, Lipstick and Clayland.

Tipically Desktop environments create their own Wayland compositors which is tipically embeded within their windows manager for example Kwin and Mutter.

For legacy X11 with no support for Wayland we can use XWayland software that is available in the Weston package. Allows X-dependent apps to run oon the X server and display via a Wayland session.

Troubleshoot Wayland:

* Try GUI without Wayland
* Using GNOME:
    * Edit /etc/gdm3/custom.conf
    * Remove the # from the WaylandEnable=fase line and save the file
    * Reboot the system and log in to a GUI session.
* Check graphics card: On the vendors website check if Wayland is suported
* Use different compositor: If using a desktop environment built-in compositor try using Weston compostor.

Some desktop environment commands won't work when you have a Wayland session. For example: `gnome-shell --replace`

## Managing the GUI

Desktop environment provides a pre-determined look and feel to the GUI. It is tipically broken up into the following graphical sections and functions:

* Desktop settings
* Display Manager
* File Manager
* Icons
* Favorites Bar
* Launch
* Menus
* Panels
* System Tray
* Widgets
* Windows Manager

### The X GUI Login System

Display manager is the login screen basically. Allows to select different desktop environments and prompts a login.

Every linux display manager package uses the X Display Manager Control Protocol (XDMCP) to handle the graphical login process.

The X Display Manager (XDM) package is the basic display manager software available for linux.

To make config changes: `/etc/X11/xdm/xdm-config`

Most window managers create their own display manager:
* KDM
* GDM
* LightDM: Xfce

## Common Linux Desktop Environments

### Getting to Know GNOME

Display manager: GNOME Display Manager (GDM)
File Manager: GNOME Files. Formerly called Nautilus
Favorites Bar: GNOME Shell Dash, sometimes called Dock
Panels: A single panel located at GNOME Shell frame's top
System Tray: Located on the right-hand side of the single panel
Windows Manager: Mutter

### Probing KDE Plasma

Display manager: SDDM (Simple Desktop Display Manager)
File Manager: Dolphin
Favorites Bar: Displayed inside Application menu
Panels: A single panel located at the Plasma frame's bottom
System Tray: Located on the right side of the sinvle panel
Widgets: Called Plasmoids
Windows Manager: Kwin

### Considering Cinnamon

Display manager: LightDM
File Manager: Nemo (a fork of Nautilus)
Favorites Bar: Displayed inside Application menu
Panels: A single panel called the Cinnamon pannel located at the Cinnamon frame's bottom
System Tray: Located on the right side of the single panel
Widgets: Cinnamon Spices
Windows Manager: Muffin (a fork of GNOME Shell's Mutter)

### Making Acquaintance with MATE

Display manager: LightDM
File Manager: Caja (a fork of Nautilus)
Favorites Bar: A Favorites Menu is used instead and is accessed via the Applications menu-driven launcher
Panels: One panel at the bottom of the MATE frame and the otherpanel at the top of the MATE UI
System Tray: Located on the right side of the top panel
Windows Manager: Marco (a fork of Metacity)

### Going Bare-Bones with Xfce

Display manager: LightDM
File Manager: Thunar
Favorites Bar: A single icon at the left side of the panel displays favorites, recent applications, and the application menu
Panels: A single panel located at the top of the window
System Tray: A set of icons on the right side of the panel
Windows Manager: The specialized Xfwm, which utilizes its own compositor manager

## Providing Accessibility

For people with problems like blind or only one hand, deaf etc we can tweak accessibility settings.

Visual impairment accessibility settings:

* Cursor Blinking
* Cursor Size
* High Contrast
* Large Text
* Screen Reader: Popular choices include Orca and Emacspeak
* Sound Keys
* Zoom

If a blind has access to a braille display we can install the brltty package. This package operates as a Linux daemon and provides console access via a braille display. We can also use orca with a refreshable braille display.

Hand and finger impairment accessibility settings:

* Bounce keys
* Double-Click delay
* Gestures
* Hover Click
* Mouse Keys
* Repeat Keys
* Screen Keyboard
* Simulated Secondary Click
* Slow Keys
* Sticky Keys

AccessX was a program that provided many of options above.

## Using X11 for Remote Access

Since X11 employs the server/client model, we can conect a client to a remote server.

### Remote X11 Connections

We can redirect the standard X11 desktop protocol stream across the network to the remote client:

* On machine 1: `xhost +machine2IP`
* On machine 1: `ssh machine2IP`
* On machine 2 using ssh conection: `export DISPLAY=machine2IP:0:0`
* Using the same command prompt launch an app
* When finished: `xhost -machine2IP`

### Tunneling Your X11 Connection

On the remote system make sure the `X11Forwarding` directive is set to yes in `/etc/ssh/sshd_config`.

After that: `ssh -X remote-host`

We can also use -Y option that takes the remote machine as a trusted X11. This is less secure.

## Using Remote Desktop Software

Common desktop implementations: VNC, Xrdp, NX and SPICE

### Viewing VNC

Employs the Remote Frame Buffer (RFB) protocol.

This protocol allows a user on the client side to send GUI commands, such as mouse clicks, to the server. The server sends desktop frames back to the client's monitor.

VNC server operates at TCP port 5900 + n where n equals to the display number.

On the client you point to the VNC server using IP + TCP port or display number.

VNC server requires authentication which is not the Linux system authentication.

The VNC server allows access from java-enable web browser in TCP port 5800 + n.

HTML5 client web browsers are suported as well.

Two types of desktop UIs for VNC clients:

* Persistent: When apps are opened, if you left and then come back and unlock the screen, the GUI is in the same state it was left.

* Static: No saved state GUI. When you left and come back, a default desktop is shown.

Positive benefits of VNC:

* A lot of flexibility in providing remote desktops
* Desktops are available for multiple users
* Both persistent and static desktops are available
* It can provide desktops on an on-demand basis
* An SSH tunnel can be employed via ssh or a client viewer command-line option to encrypt traffic

Potential difficulties or concerns with VNC:

* Handle sonly mouse movements and keystrokes. Does not hanlde file and audio transfer or printing services for the client
* No traffic encryption by default so OpenSSH tunneling needs to be set up
* VNC server password is saved in plain text in the server config file

Alternatives that implement the VNC technology: TigerVNC

TigerVNC works on Linux and Windows.

To install it we need to install the tigervnc-server package name.

For the client we need to install the tigervnc package.

Server commands: vncserver and vncconfig
Cliend commands: vncviewer

Example client command: `vncviewer example.com:1` being 1 the display number.

When employing the VNC server we need to make sure to employ openSSH port forwarding.

### Grasping Xrdp

Uses Remote desktop protocol (RDP) and uses X11rdp or Xvnc to manage the GUI session.

Only provides server side. For the client we can use FreeRDP, rdesktop and Microsoft Remote Desktop Connection.

Xrdp comes system ready. After installing we can enable the port 3389 on the firewall to allow access.

Configuration file: `/etc/xrdp/xrdp.ini`

We can modify `security_layer` in xrdp config file to set the transport security:

* negotiate: Is the default option and sets the security method to the highest the client can use
* tls: Uses SSL encryption
* Rdp: Sets the method to standard RDP security. Not safe from man-in-the-middle attacks

### Exploring NX

This protocol was opensource but later in time became closed source.

Benefits:

* Fast response times thanks to compressed X11 data and caching
* Faster than VNC-based products
* OpenSSH tunneling by default
* Support for multiple simultaneous users through a single network port

Open source variations: FreeNX and X2Go

### Studying SPICE

Primary focus is providing remode desktop access to virtual machines.

Spice is platform independent.

Features:

* Spice's client side uses multiple data socket connections, and you can have multiple clients.
* Spice delivers desktop experience speeds similar to a local connection.
* Spice consumes low amounts of CPU
* Spice allows high-quality video streaming
* Spice provides live migration features

Spice has a single server implementation but several clients: remote-viewer and GNOME Boxes

Strong security features: Can encrypt traffic using TLS. Auth implemented using Simple Authentication and Security Layer (SASL) so Kerberos can be used.

For X11 we can use X.Org-created Xspice to act as a standalone Spice server as well as an X server.

## Understanding Localization

### Character Sets

A character set defines a standard code used to interpret and display characters in a language:

* ASCII
* ISO-8859: From ISO-8859-1 through ISO-8859-15
* Unicode
* UTF

### Environment variables

Use `locale` to print the environment variables that contain locale settings.

Env locale format: `language_country.character set` for example `en_US.UTF-8`.

Each `LC_` env variable represent a category of more environment variables that relate to the locale settings.

To explore those variables: `locale -ck LC_MONETARY` this shows the variables in the LC_MONETARY environment variable.

## Setting Your Locale

Three components to how Linux handles localization: language, country and character set.

### Installation Locale Decisions

On the installation of the OS, a window will ask you to specify the country. After that the installation script automatically sets the environment variables.

### Changing Your Locale

You can eather change the environment variable manually or employ `localectl`.

```bash
export LANG=
locale
```

The `LANG` environment variable will set the value to all `LC_*` environment variables.

This only works for the current shell session. To preserve we need to add it on the `.bashrc` file so it runs on every login.

With localectl:

```bash
localectl
   System Locale: LANG=es_ES.UTF-8
       VC Keymap: n/a
      X11 Layout: es
       X11 Model: pc105

localectl set-locale LANG=en_GB.UTF-8
```

We can use `list-locales` to list installed locales and `set-locale` to set the locale.

To convert a file from one charset to another we can employ the `iconv` command.

## Looking at time

Linux handles the time as two parts:

* The time zone asociated with the location of the system
* The actual time and date within that time zone

### Working with Time Zones

Local time zone in `/etc/timezone` for debian systems and `/etc/localtime` for Red Hat-based systems.

Can't mod this files. Instead we copy a template from `/usr/share/zoneinfo`

To determine the current time zone setting: `date`

To change time zone:

```bash
sudo mv /etc/localtime /etc/localtime.bak
sudo ln -s /usr/share/zoneinfo/US/Pacific /etc/localtime
```

We can employ `tzselect` to know the timezone based on some questions.

To change the time zone only for the current session we can just change the `TZ` environment variable.

### Setting the Time and Date

#### Legacy commands

`hwclock`: Display or set the time as kept on the internal BIOS or UEFI clock
`date`: Displays or sets the date as kept by the Linux system

With date we can display the current time with the format we cant employing the `+` option.

```bash
date +"%A, %B %d %Y"
    lunes, abril 22 2024
```

We can also set the time and date on the Linux system:

```bash
date MMDDhhmm[[CC]YY][.SS]
```

#### The timedatectl Command

If the system uses the systemd set of utilities we can emply `timedatectl`.

By default it shows thetim and date settings.

hardware clock is called RTC.

To modify any of the settings seen in the output:

```bash
sudo timedatectl set-time "2019-08-02 06:15:00"
```

If the Linux system is conected to a NTP Server we won't be able to alter the time or date using either `date` or `timedatectl`.

## Configuring Printing

Common Unix Printing System (CUPS) provies a common interface for working with any type of printer.

Accepts jobs using the PostScript document format and sends them to printers using a print queue system.

There can be multiple queues assigned to a single printer or multiple printers can accept jobs assigned to a single print queue.

CUPS uses the Ghostscript program to convert PostScript document into a format understood by different printers.

Config file: `/etc/cups`

Exposed in port TCP 631.

We can also configure network printers using Internet Printing Protocol (IPP) or the Microsoft Server Message Block (SMB) protocol.

We can use either the web interface or command-line tools to interact with CUPS.

Commands:

* `cancel`: Cancels a print request
* `cupsaccept`: Enables queuing of print requets
* `cupsdisable`: Disables he specified printer
* `cupsenable`: Enables the specified printer
* `cupsreject`: Rejects queuing of print requests

CUPS also accepts legacy BSD commad-line printing utility:

* `lpc`: Start, stop, or pause the print queue
* `lpq`: Display the print queue status, along with any print jobs waiting in the queue
* `lpr`: Submit a ew print job to a print queue
* `lprm`: Remove a specific print job from the print queue

Example of use:

Show status, send a job and then check the status again.

```bash
lpq -P EPSON
lpr -P EPSON test.txt
lpq -P EPSON
```