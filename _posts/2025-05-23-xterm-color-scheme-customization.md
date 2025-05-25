---
title: "XTerm color scheme customization"
date: 2025-05-23
---
## Introduction
This post shows how to implement color schemes for XTerm.  The schemes
can then be selected with the `customization` resource.  This is an
efficient method to support a large number of color schemes.

## Requirements
* cpp: GNU C preprocessor
  ```sh
  sudo apt install cpp
  ```
* curl: command line tool for transferring data with URL syntax
  ```sh
  sudo apt install curl
  ```

## Setup
The setup steps that follow will use scripts and configuration files
that are specific to the `IceWM` window manager. It should not be
difficult to adapt to other environments.

### Load .Xdefaults
In .Xdefaults (or .Xresources), define *base* parameters that all XTerm
instances should share.  But resources related to colors must **not** be
included.  For example, a .Xdefaults file might include some of these
resources.

* $HOME/.Xdefaults
  ```
  XTerm*termName: xterm-256color
  XTerm*eightBitInput: false
  XTerm*locale: true
  XTerm*utf8Latin1: true
  XTerm*showBlinkAsBold: true
  XTerm*utmpInhibit: true
  XTerm*reverseWrap: true
  XTerm*scrollBar: true
  XTerm*rightScrollBar: true
  XTerm*saveLines: 10000
  XTerm*scrollTtyOutput: false
  XTerm*scrollKey: true
  XTerm*metaSendsEscape: true
  XTerm*charClass: 33:48,37:48,45-47:48,64:48
  XTerm*on3Clicks: regex [[:alpha:]]+://([[:alnum:]!#+,./=?@_~-]|(%[[:xdigit:]][[:xdigit:]]))+
  XTerm*printAttributes: 0
  XTerm*printerCommand: cat > /dev/shm/xterm.log
  ```

Load $HOME/.Xdefaults into the root window when an x session starts up.

* $HOME/.icewm/startup
  ```sh
  xrdb -load $HOME/.Xdefaults
  ```

### Override environment variables
Override these two environment variables when an x session begins.  Only
XUSERFILESEARCHPATH is required.  The example uses $HOME/X11/XAPPLRESDIR
but of course change it to fit your taste.  Be sure to create the
directory if it doesn't already exist.

* $HOME/.icewm/env
  ```sh
  XENVIRONMENT=/dev/null
  XUSERFILESEARCHPATH=$HOME/X11/XAPPLRESDIR/%N%C:$HOME/X11/XAPPLRESDIR/%N
  ```

Description of these two variables from x man page:
* XENVIRONMENT
  ```
  This must point to a file containing X resources. The default is
  $HOME/.Xdefaults-<hostname>. Unlike /usr/lib/X11/Xresources, it is
  consulted each time an X application starts.
  ```
* XUSERFILESEARCHPATH
  ```
  This must contain a colon separated list of path templates, where libXt
  will search for user dependent resource files. The default value is:
  $XAPPLRESDIR/%L/%N%C:\
  $XAPPLRESDIR/%l/%N%C:\
  $XAPPLRESDIR/%N%C:\
  $HOME/%N%C:\
  $XAPPLRESDIR/%L/%N:\
  $XAPPLRESDIR/%l/%N:\
  $XAPPLRESDIR/%N:\
  $HOME/%N
  $XAPPLRESDIR defaults to $HOME, see below.
  A path template is transformed to a pathname by substituting:
  
  %N => name (basename) being searched for
  %T => type (dirname) being searched for
  %S => suffix being searched for
  %C => value of the resource "customization"
        (class "Customization")
  %L => the locale name
  %l => the locale's language (part before '_')
  %t => the locale's territory (part after '_' but before '.')
  %c => the locale's encoding (part after '.')
  ```

### Create color scheme files
A large collection of color schemes/themes can be found here.
```
https://github.com/janoamaral/Xresources-themes
```

Let's use these 3 color schemes in this example.
```
https://raw.githubusercontent.com/janoamaral/Xresources-themes/refs/heads/master/base16-phd-256.Xresources
https://raw.githubusercontent.com/janoamaral/Xresources-themes/refs/heads/master/iterm-Novel.Xresources
https://raw.githubusercontent.com/janoamaral/Xresources-themes/refs/heads/master/iterm-Mathias.Xresources
```

Some color themes define colors with `#define`, such as the first one
in the list (base16-phd-256.Xresources).  Therefore `cpp` is used to
process those files before they can be used.

Run this script to process the themes into proper resource files.
```sh
BASEURL=https://raw.githubusercontent.com/janoamaral/Xresources-themes/refs/heads/master
OUTDIR=$HOME/X11/XAPPLRESDIR

mkdir -p $OUTDIR

for theme in base16-phd-256.Xresources iterm-Novel.Xresources iterm-Mathias.Xresources
do
    curl -s $BASEURL/$theme | cpp > $OUTDIR/XTerm-${theme%.Xresources}
done
```

The script should create these files.  It is important that a filename
start with `XTerm` and is followed by a name.  The name can be any valid
string.
```
$HOME/X11/XAPPLRESDIR/XTerm-iterm-Novel
$HOME/X11/XAPPLRESDIR/XTerm-iterm-Mathias
$HOME/X11/XAPPLRESDIR/XTerm-base16-phd-256
```

Now suppose `base16-phd-256` should be the default color theme.  Create this file.
```sh
echo '#include "XTerm-base16-phd-256"' > $HOME/X11/XAPPLRESDIR/XTerm
```

Now, there should be four files.
```
$HOME/X11/XAPPLRESDIR/XTerm
$HOME/X11/XAPPLRESDIR/XTerm-iterm-Novel
$HOME/X11/XAPPLRESDIR/XTerm-iterm-Mathias
$HOME/X11/XAPPLRESDIR/XTerm-base16-phd-256
```

### Restart x session
Restart the x session or reboot.  It is important that the environment
variables are set and that the root window does not store any XTerm
resource related to colors.

## Usage
* To use the default theme (ex: *base16-phd-256*)
  ```sh
  xterm
  ```
* To use the *iterm-Novel* theme
  ```sh
  xterm -xrm '*customization:-iterm-Novel'
  ```
* To use the *iterm-Mathias* theme
  ```sh
  xterm -xrm '*customization:-iterm-Mathias'
  ```
