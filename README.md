Description
----------------------------
The below enables OSX style copy and paste between a headless X11 desktop (running on a Linux VPS using
fluxbox WM and tightvncserver) and OSX using an OSX VNC client.

It works by remapping X11 keys used by xterm and X11 apps to match OSX command-c and command-v copy and
paste. Also enables VNC clients to copy and paste between the OSX and X11 desktops. Note that copy paste
works both ways when using the RealVNC OSX client, but with Apple's Screen Sharing you can only paste an
X11 copy to the OSX desktop (not vise versa) because it doesn't like autocutsel or Linux or something.

VPS X11 and VNC Server files
----------------------------
\~/.Xresources

    XTerm*faceName: Terminus:style=Regular:size=14
    XTerm*Background: grey3
    XTerm*Foreground: chartreuse1

    ! Make xterm use CLIPBOARD instead of PRIMARY so ctrl-v works in other X11 apps from xterm copies.
    ! xterm is apparently one of the few modern apps that does not use CLIPBOARD by default.
    ! Note that xterm will automatically copy selections to CLIPBOARD, so no need to remap ctrl-c (which
    ! is needed to break anyway). Also make xterm use ctrl-v for paste instead of Shift-Insert (which again
    ! is peculiar to xterm since other X11 apps use ctrl-v).
    XTerm*selectToClipboard: true
    XTerm*VT100.translations: #override \n \
      ~Meta Ctrl <Key>V: insert-selection(CLIPBOARD) \n \
      ~Meta Ctrl <Key>N: spawn-new-terminal()

\~/.vnc/xstartup

    #!/bin/sh

    # Map OSX command-c and command-v to X11 ctrl-c and ctrl-v
    # Alt_L and Super_L are what xev reports osx command keys (left and right) as mapped to.
    # Add them to xmodmap's control modifier.
    xmodmap -e "clear control"
    xmodmap -e "keycode 14 = Alt_L" #RealVNC default OSX Left Command Key
    xmodmap -e "keycode 89 = Super_L" #RealVNC default OSX Right Command Key
    xmodmap -e "add control = Control_L Control_R Alt_L Super_L"
    
    # Enable VNC client to copy and paste to/from VPS's clipboard.
    # Otherwise, copy and paste works just within the X11 desktop manager.
    autocutsel -s CLIPBOARD -fork

    xrdb $HOME/.Xresources
    xsetroot -solid grey
    xterm &
    /etc/X11/Xsession

pdanford - May 2015. MIT License, etc.
