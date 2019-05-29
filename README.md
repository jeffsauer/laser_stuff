# laser_stuff
50W Laser Engraver and Cutter

Instead of the cheaper 40W machines I've seen on eBay, many sources recommended the 50W machine instead.  More powerful laser, better build quality of the machine itself, height adjustable bed, etc.  Including shipping, the 50W machines can be had for around $1500. 

# Videos

This video was really helpful: https://www.youtube.com/watch?v=_qP0ekfRWXE

And he pointed to this extremely useful video playlist specific to the machine I purchased: https://www.youtube.com/playlist?list=PLeKaKWOIPgi_XP6Nxzeapp-vUMCLQyVMB

# Software

As for software, I found a forum specific to the RDWorks software at: https://rdworkslab.com/viewtopic.php?f=107&t=3485

And downloaded the latest version (at the time of this writing, 8.01.40-190341): http://www.rabbitlaserusa.com/DriverDisk/Ruida/

This software is for Windows... I was going to use Wine to run it, but after trying out the latest Windows 10 build 1903 on the HP Stream 14-ax00x (dual core celeron N3060, 4GB RAM, 32GB storage), it actually ran quite well. So, I'll keep the Wine information below, but for now I've decided to run Windows 10 and run the RDWorks software natively.  The HP Stream will still be used primarily to control the cutter and that is it.

# Running RDWorks in Wine on Linux

To get RDWorks running in Wine, I first installed the latest wine/winehq from the standard linux mint repository, and then used this shell script: https://aur.archlinux.org/cgit/aur.git/tree/rdworks-wine-bin?h=rdworks-wine-bin

```
#!/bin/bash

export WINEPREFIX="$HOME/.rdworks-wine-bin"
export WINEARCH=win32
export WINEDEBUG=fixme-all
export WINEDLLOVERRIDES="mshtml="

function setpath(){
    # PathExt
    keyname="HKLM\System\CurrentControlSet\Control\Session Manager\Environment"
    valuename="PATHEXT"
    value="$(wine reg query "$keyname" -v "$valuename" | sed 's|\r||g' | awk  '$1 == "PATHEXT" {print $3 ";."}')"
    wine reg add "$keyname" /f /v "$valuename" /t REG_SZ /d "$value"
    # Path
    keyname="HKLM\System\CurrentControlSet\Control\Session Manager\Environment"
    valuename="PATH"
    value="$(wine reg query "$keyname" -v "$valuename" | sed 's|\r||g' | awk  '$1 == "PATH" {print $3}')$(echo $(for i in $(echo $PATH | sed 's|:|\n|g') ; do echo -n \;$(winepath -w $i) ; done 2>/dev/null))"
    wine reg add "$keyname" /f /v "$valuename" /t REG_SZ /d "$value"
}

function setup(){
    mkdir -p `dirname $WINEPREFIX`
    setpath
    winetricks -q mfc42 cmd
}

if [ ! -d "$WINEPREFIX" ] || [ "-s" = "$1" ] ; then
    setup
    wine "/home/jsauer/bin/RDWorksV8Setup8.01.40-190341.exe" "`winepath -w "$@"`"
fi

# Run the actual rdworks
wine "$WINEPREFIX/drive_c/RDWorksV8/RDWorksV8.exe" "`winepath -w "$@"`"
```

# Tentative Workflow

For design work, I plan to use the Inkscape vector drawing app which is surprisingly good... been using it for vinyl cutting and 3D printing designs. Basic design work to be done in Inkscape, export as DXF file, import DXF into RDWorks, position, define which layers are for cutting and which are for engraving / scanning, etc. Then save from RDWorks to a .rld file for export to the laser machine. Laptop on network, with a shared folder, so I can simply copy the .rld file via the network from my design machine in the office over to the laptop attached to the cutter in the shop.

The RDWorks software installed on the HP Stream is just for outputting / final tweaking before cutting.  On a more powerful machine, a VirtualBox instance running Windows 10 with the RDworks software installed will be used for most tweaking.

As for accessories, safety first.  Proper eye safety with CO2 spec'd glasses, and also a fire extinguisher nearby.  Proper ventilation is also very important.

# Pictures

![s-l1600.jpg](https://github.com/jeffsauer/laser_stuff/blob/master/s-l1600.jpg)
![s-l16001.jpg](https://github.com/jeffsauer/laser_stuff/blob/master/s-l16001.jpg)
![s-l16002.jpg](https://github.com/jeffsauer/laser_stuff/blob/master/s-l16002.jpg)
![s-l16003.jpg](https://github.com/jeffsauer/laser_stuff/blob/master/s-l16003.jpg)
![s-l16004.jpg](https://github.com/jeffsauer/laser_stuff/blob/master/s-l16004.jpg)
![s-l16005.jpg](https://github.com/jeffsauer/laser_stuff/blob/master/s-l16005.jpg)
![s-l16006.jpg](https://github.com/jeffsauer/laser_stuff/blob/master/s-l16006.jpg)
![s-l16007.jpg](https://github.com/jeffsauer/laser_stuff/blob/master/s-l16007.jpg)
![s-l16008.jpg](https://github.com/jeffsauer/laser_stuff/blob/master/s-l16008.jpg)
