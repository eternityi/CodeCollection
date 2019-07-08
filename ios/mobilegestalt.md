# MobileGestalt

The _libMobileGestalt.dylib_ provides a central repository for all of the iOS's properties. It can be analogous to OS X's Gestalt, which is part of `CoreServices`. OS X's Gestalt is documented for example [Gestalt Manager](https://developer.apple.com/documentation/coreservices/carbon_core/gestalt_manager) and has been deprecated as of 10.8. **MobileGestalt** is entirely undocumented by Apple as it is a private library.

MobileGestalt allows for the testing of system properties that may or may not be compatible on different simulators.

Quite a few system processes and apps in iOS rely on MobileGestalt, which is located at /usr/lib/libMobileGestalt.dylib. It's more of a basic library, but its exposed APIs follow the Apple framework conventions and uses the `MG` API prefix for example `MGIsDeviceOneOfType`.

If you look for MobileGestalt on the iOS filesystem you won't find it - like all private frameworks and libraries, it has been prelinked into the /System/Library/Caches/...etc. If you like hacking and pen-testing then you can use tools to extract it.

MobileGestalt provides plenty of information - around 200 or so queries - on various aspects of the system. Here are a few. 

```objectivec
libMobileGestalt.dylib
//Answers to MG queries

MGCopyAnswer(@"5MSZn7w3nnJp22VbpqaxLQ");
MGCopyAnswer(@"7mV26K/1a+wTtqiunvHMUQ");
MGCopyAnswer(@"BasebandAPTimeSync");
MGCopyAnswer(@"BasebandPostponementStatus");
MGCopyAnswer(@"BasebandPostponementStatusBlob");
MGCopyAnswer(@"BasebandSecurityInfoBlob");
MGCopyAnswer(@"BasebandStatus");
MGCopyAnswer(@"BuildVersion");
MGCopyAnswer(@"CoreRoutineCapability");
MGCopyAnswer(@"DeviceClass");
MGCopyAnswer(@"DeviceClassNumber");
MGCopyAnswer(@"DeviceName");
MGCopyAnswer(@"DeviceSupports1080p");
MGCopyAnswer(@"DeviceSupports720p");
MGCopyAnswer(@"DiskUsage");
MGCopyAnswer(@"GSDeviceName");
MGCopyAnswer(@"HWModelStr");
MGCopyAnswer(@"HasBaseband");
MGCopyAnswer(@"InternalBuild");
MGCopyAnswer(@"InverseDeviceID");
MGCopyAnswer(@"IsSimulator");
MGCopyAnswer(@"MLBSerialNumber");
MGCopyAnswer(@"MaxH264PlaybackLevel");
MGCopyAnswer(@"MinimumSupportediTunesVersion");
MGCopyAnswer(@"PasswordConfigured");
MGCopyAnswer(@"PasswordProtected");
MGCopyAnswer(@"ProductType");
MGCopyAnswer(@"ProductVersion");
MGCopyAnswer(@"RegionCode");
MGCopyAnswer(@"RegionalBehaviorNTSC");
MGCopyAnswer(@"RegionalBehaviorNoPasscodeLocationTiles");
MGCopyAnswer(@"ReleaseType");
MGCopyAnswer(@"SIMStatus");
```

There are hundreds more e.g. `AirplaneMode`, `MobileEquipmentIdentifier`, etc. 

MobileGestalt maintains a table of OSType selector codes. for example **c:890** in the message: **libMobileGestalt MobileGestalt.c:890: MGIsDeviceOneOfType is not supported on this platform.** In this case `MGIsDeviceOneOfType` is a method of the **MobileGestalt library**.

Instead of checking the simulator version there is a separate selector for directly querying the capabilities of the simulator. The messages most likely indicate incompatibilities between simulator versions and Xcode versions and/or unsupported APIs on the simulator.

