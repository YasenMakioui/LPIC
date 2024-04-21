# 6. Configuring the gui, loaclization, printer

## UNDERSTANDING THE GUI

GUI components:

Desktop environment (-) Windows manager (-) Display server

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
