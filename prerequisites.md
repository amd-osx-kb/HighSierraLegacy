# Prerequisites

This guide focuses on desktops as integrated AMD graphics sadly do not work in macOS. This means that laptop will not have any acceleration \(same goes for APUs, unless you add a separate GPU\).   
So far this guide has only been tested on Ryzen CPUs.

What will you need to make an installer:

* an 8GB or larger USB drive
* Install macOS.app \(Preferably from the Appstore\)
* [Clover installer](https://github.com/Dids/clover-builder/releases) \(Daily builds done courtesy of Dids\)
* [Clover Configurator ](http://mackie100projects.altervista.org/download-clover-configurator/)\(You can also use any text editor, but CC is generally a lot faster\)
* [FakeSMC.kext](https://bitbucket.org/RehabMan/os-x-fakesmc-kozlek/downloads/) \(The one kext you absolutely need. It emulates the SMC of a Mac and basically tells macOS "Yes this is a real mac, you are free to boot"\)
* Any kexts that are needed for your specific hardware
* A cup of coffee \(or a few\)
* Patience
* Some good Google-fu



