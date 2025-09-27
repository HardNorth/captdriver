# captdriver - alternative driver for Canon CAPT printers with macOS support

**Captdriver an alternative driver for Canon laser printers**
that only support the proprietary CAPT communications protocol
and associated data stream formats.

It aims to be a portable and reliable driver that can extend the
service life of existing CAPT-only printers by extending support
to more platforms and newer operating systems.

## Installation and Information

Please check the [Captdriver Wiki](https://github.com/mounaiban/captdriver/wiki)
for a list of [supported devices](https://github.com/mounaiban/captdriver/wiki#supported-devices),
[installation instructions](https://github.com/mounaiban/captdriver/wiki/Building-and-Installing-captdriver%3A-A-Unified-Guide),
project status, technical information and links to more resources.

## Installation on macOS

Begin by preparing a suitable build environment on your Mac:
* **Install Xcode Command Line Tools**: This provides compilers (clang) and build utilities. You can install it by
  running `xcode-select --install` in Terminal.
* **Install Build Dependencies**: The captdriver project uses the GNU Autotools build system. Ensure you have autoconf,
  automake, libtool, and possibly pkg-config installed. If these are not present on your Mac, you can install them via
  [Homebrew](https://brew.sh/): `brew install autoconf automake libtool pkg-config`.

Next, retrieve the source code and navigate to this folder:
```shell
git clone https://github.com/mounaiban/captdriver.git
cd captdriver
```

After this, build `captdriver`:
```shell
aclocal
autoconf
automake --add-missing
./configure
make
ppdc -v -d . src/canon-lbp.drv
```

Copy CUPS Filter driver to system's CUPS Filter CUPS filter directory:
```shell
sudo cp -p src/rastertocapt $(cups-config --serverbin)/filter/
```

Install the PPD File: copy the `CanonLBP-2900-3000.ppd` or `CanonLBP-3010-3018-3050.ppd` into the system PPD directory.
macOS expects PPD files in `/Library/Printers/PPDs/Contents/Resources/` (and within an *.lproj subfolder for
localization, e.g. en.lproj for English). If these folders don’t exist, create them:
```shell
sudo mkdir -p /Library/Printers/PPDs/Contents/Resources/en.lproj
sudo cp CanonLBP-*.ppd /Library/Printers/PPDs/Contents/Resources/en.lproj/
```

Ensure the files have proper permissions (owner root:wheel, and readable by all). For example:
```shell
sudo chown root:wheel $(cups-config --serverbin)/filter/rastertocapt
sudo chmod 755 $(cups-config --serverbin)/filter/rastertocapt
```

Now install the printer:
1. Connect the Printer: Attach the LBP-2900 via USB. Power it on. macOS should detect a USB printer.
2. Add a New Printer: Open System Preferences (or System Settings on macOS 13+), go to Printers & Scanners, and click 
   the "+" (or "Add Printer, Scanner, or Fax...") button to add a printer. In the add printer dialog, you should see the
   Canon printer listed as a USB printer. Select it.
3. Choose the Driver (PPD): In the "Use:" drop-down, select "Select Software…". A list of printer software (PPDs)
   installed on the system will appear. If you placed the PPD correctly, you should find an entry matching your Canon
   printer (for example, "Canon Inc LBP2900/LBP3000 r2c, 0.1.4" – this comes from the PPD’s NickName). Choose that. If
   you don’t see it in the list, click "Other…" and navigate to the PPD file (e.g., in 
   /Library/Printers/PPDs/Contents/Resources/en.lproj/). Select the PPD manually.
4. Finish Installation: Confirm adding the printer with "Add" button.

macOS will create a new print queue using the PPD and our filter. You can verify this by checking the printer’s
"Options & Supplies" in System Preferences – it should list the driver as the one you provided.

You are done!

Additionally, you can find error and access log in these two files:
* `/var/log/cups/error_log`
* `/var/log/cups/access_log`

## Legal Information

Captdriver is Free Software, licensed under the [terms and conditions](https://www.gnu.org/licenses/gpl-3.0.html)
of the [GNU General Public License, Version 3](https://choosealicense.com/licenses/gpl-3.0/)

This is unofficial software not endorsed or authorised by Canon Inc.
and/or its affiliates.
