# Preparing the installer

### Copying the kernel

macOS will not be able to boot on AMD systems in this configuration. For that to work we need to replace a few files.

The new Kernel will need to be placed in the _System/Library/Kernels_ folder of the installer USB. This folder does not exist by default, so you need to go to _System/Library_ and create the folder _Kernels_. 

```bash
cd /Volumes/Mac OS X Base Installer/System/Library
mkdir Kernels
cp ~/Desktop/kernel kernel
```

The first command changes the current directory to _/Library_  
The second command creates the _Kernels_ directory  
The third command copies the Kernel from the desktop

### Copying some kexts

The BaseSystem image does not have all the kexts that we need to boot macOS on AMD. We will need to have to add those ourselves from an existing High Sierra install. I will look a bit more deeply in to what kexts are truly needed, but from what I managed to find in some online research and trial/error:

* AppleActuatorDriver.kext
* AppleSMCRTC.kext
* AppleUSBCommon.kext
* IOSlaveProcessor.kext
* KernelRelayHost.kext
* IOUSBHostFamily.kext

If you are using an FX system you will need to download the following files as well:

* DummyUSBEHCIPCI.kext
* DummyUSBXHCIPCI.kext

These files can be downloaded here. We do not need them on Ryzen as we will be setting up native USB. This will be explained later on.

#### Side note:

Do not add your other kexts here. We will add those later to Clover.

### Rebuilding the prelinkedkernel

macOS does not use the kernel file to boot. Instead it uses the prelinkedkernel. This is a file containing the Kernel along with a cache of kexts. Because of this in order to be able to boot we need to rebuild it. First we will have to rebuild the permissions. 

Open a Finder window and right click on your USB drive. Press `Get Info`. On the very bottom of the window that just opened press on the lock and enter your password to unlock some extra settings. Now uncheck `Ignore Ownership` from the now no longer greyed out options at the bottom of the page.

Now that this is done, open a terminal window again and execute the following commands:

```bash
cd /Volumes/BaseSystem/System/Library/
sudo chown -R 0:0 Extensions
sudo chmod -R 755 Extensions/*.kext
sudo xattr -c Extensions/*
sudo touch Extensions
sudo rm /Volumes/BaseSystem/System/Library/PrelinkedKernels/prelinkedkernel
sudo kextcache -u /Volumes/BaseSystem
```

This process can take a few minutes and when it is done you will most likely receive an error complaining about `com.apple.kext.caches/Startup`, as long as you also receive a CacheID this is fine.

The first 5 commands are used to repair permissions on the kexts. The first command changes directory to the Library folder. The second one changes the owner of all files in the `Extensions` folder to root.The third one changes the permissions of all kexts in the Extensions folder so that the file owner has full control. The command after that strips all extra attributes the files might have. The last of this section of commands basically updates the modification date of all files in the Extensions folder.

The last two commands are used to rebuild the prelinkedkernel. The first one removes the existing file and the second one is what rebuild the kernel cache aka the prelinkedkernel.

### Installing Clover

For this next part we will be installing the bootloader _Clover_ to the USB drive so that it can be booted on regular computers, not just Mac's.  
Let's get started.

Launch your Clover installer that you downloaded in the prerequisites section. On the second page of the installer **make sure to select your USB drive!** The default settings set by Clover are not all that desirable so we need to **Customise** the install.

You want to use the following settings, as shown in the screenshots. This is generally all that's needed.  


* _Install Clover for UEFI booting only_
* _Install Clover to the ESP_
* Under _Drivers64UEFI:_
  * _AptioMemoryFix_ \(the new hotness that includes NVRAM fixes, as well as better memory management\)
  * _VBoxHfs-64.efi_ \(or _HFSPlus.efi_ if available\) - one of these is required for Clover to see and boot HFS+ volumes. If you see the option to enable it in the installer, make sure it's selected - if you don't see it in the installer, verify that one of them exists in the _EFI -&gt; CLOVER -&gt; drivers64UEFI_ folder
  * _ApfsDriverLoader_ - \(Available in Dids' Clover builds - or [here](https://github.com/acidanthera/ApfsSupportPkg/releases)\) this allows Clover to see and boot from APFS volumes by loading apfs.efi from ApfsContainer located on block device \(if using AptioMemoryFix as well, requires R21 or newer\)

Provided you don't plan on using FireVault \(which I don't even think works properly on AMD\) this concludes our clover installation!

### Copying kexts

When Clover is done installing a drive named EFI will pop up on your desktop. Open this and navigate to  _/Volumes/EFI/EFI/CLOVER/kexts/Other_ and copy all your .kext files that you downloaded in the Kexts section here. 

You will also see other folders named 10.xx in the _/kexts/_ directory. These are not needed for our goal. These are used for version specific kexts in case you are multibooting multiple macOS versions.

## Next up:

### Setting up Clover.

First some theory on how it all works, and after that I will show a few different configs that should work as a basis for most systems.

