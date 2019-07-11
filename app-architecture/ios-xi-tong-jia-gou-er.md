# iOS系统架构（二）

## iOS系统架构回顾

[![](https://raw.githubusercontent.com/Lobster-King/AppArticles/master/Architecture/ios-architecture.png)](https://raw.githubusercontent.com/Lobster-King/AppArticles/master/Architecture/ios-architecture.png)

如上图所示，经典的iOS系统架构分为四层，自下而上分为核心操作系统层、核心服务层、媒体层、用户交互层。

* **Cocoa Touch**：提供与用户交互的相关能力，包括触摸等，最常用的UIKit库就在该层；除此之外还有MapKit、Address BookUI、PhotosUI等。
* **Media Layer**：提供图形与音视频相关功能的接口，例如Core Animation、OpenGL ES等。
* **Core Services**：最常用的有Core Foundation、Foundation、CFNetwork、CoreData等。提供最基础的能力，比如数组、字典、HashMap、套接字等基础数据结构。
* **Core OS**：包含Mach、Kernel、BSD、Socket以及Sandbox等，它主要提供了底层操作的接口，对于应用开发来讲直接用到的不是很多。

## iOS系统所提供的库



### App Frameworks

* [AppKit](https://developer.apple.com/documentation/appkit)
* [Bundle Resources](https://developer.apple.com/documentation/bundleresources) Beta
* [Foundation](https://developer.apple.com/documentation/foundation)
* [Swift](https://developer.apple.com/documentation/swift)
* [SwiftUI](https://developer.apple.com/documentation/swiftui) Beta
* [TVML](https://developer.apple.com/documentation/tvml)
* [TVMLKit](https://developer.apple.com/documentation/tvmlkit)
* [TVMLKit JS](https://developer.apple.com/documentation/tvmljs)
* [TVUIKit](https://developer.apple.com/documentation/tvuikit)
* [UIKit](https://developer.apple.com/documentation/uikit)
* [WatchKit](https://developer.apple.com/documentation/watchkit)

### Graphics and Games

* [AGL](https://developer.apple.com/documentation/agl)
* [ARKit](https://developer.apple.com/documentation/arkit)
* [ColorSync](https://developer.apple.com/documentation/colorsync)
* [Core Animation](https://developer.apple.com/documentation/quartzcore)
* [Core Graphics](https://developer.apple.com/documentation/coregraphics)
* [Core Image](https://developer.apple.com/documentation/coreimage)
* [Game Controller](https://developer.apple.com/documentation/gamecontroller)
* [GameKit](https://developer.apple.com/documentation/gamekit)
* [GameplayKit](https://developer.apple.com/documentation/gameplaykit)
* [GLKit](https://developer.apple.com/documentation/glkit)
* [Image I/O](https://developer.apple.com/documentation/imageio)
* [Metal](https://developer.apple.com/documentation/metal)
* [Metal Performance Shaders](https://developer.apple.com/documentation/metalperformanceshaders)
* [MetalKit](https://developer.apple.com/documentation/metalkit)
* [Model I/O](https://developer.apple.com/documentation/modelio)
* [OpenGL ES](https://developer.apple.com/documentation/opengles)
* [PDFKit](https://developer.apple.com/documentation/pdfkit)
* [PencilKit](https://developer.apple.com/documentation/pencilkit) Beta
* [Quartz](https://developer.apple.com/documentation/quartz)
* [RealityKit](https://developer.apple.com/documentation/realitykit) Beta
* [ReplayKit](https://developer.apple.com/documentation/replaykit)
* [SceneKit](https://developer.apple.com/documentation/scenekit)
* [SpriteKit](https://developer.apple.com/documentation/spritekit)
* [Vision](https://developer.apple.com/documentation/vision)

### App Services

* [Accounts](https://developer.apple.com/documentation/accounts)
* [AddressBook](https://developer.apple.com/documentation/addressbook)
* [AddressBookUI](https://developer.apple.com/documentation/addressbookui)
* [AdSupport](https://developer.apple.com/documentation/adsupport)
* [App Store Receipts](https://developer.apple.com/documentation/appstorereceipts)
* [ApplicationServices](https://developer.apple.com/documentation/applicationservices)
* [BackgroundTasks](https://developer.apple.com/documentation/backgroundtasks) Beta
* [Business Chat](https://developer.apple.com/documentation/businesschat)
* [Business Chat REST API](https://developer.apple.com/documentation/businesschatapi)
* [CallKit](https://developer.apple.com/documentation/callkit)
* [CarPlay](https://developer.apple.com/documentation/carplay)
* [ClassKit](https://developer.apple.com/documentation/classkit)
* [ClockKit](https://developer.apple.com/documentation/clockkit)
* [CloudKit](https://developer.apple.com/documentation/cloudkit)
* [Combine](https://developer.apple.com/documentation/combine) Beta
* [Contacts](https://developer.apple.com/documentation/contacts)
* [ContactsUI](https://developer.apple.com/documentation/contactsui)
* [Core Data](https://developer.apple.com/documentation/coredata)
* [Core Foundation](https://developer.apple.com/documentation/corefoundation)
* [Core Location](https://developer.apple.com/documentation/corelocation)
* [Core ML](https://developer.apple.com/documentation/coreml)
* [Core Motion](https://developer.apple.com/documentation/coremotion)
* [Core Spotlight](https://developer.apple.com/documentation/corespotlight)
* [Core Text](https://developer.apple.com/documentation/coretext)
* [Create ML](https://developer.apple.com/documentation/createml)
* [DeviceCheck](https://developer.apple.com/documentation/devicecheck)
* [EventKit](https://developer.apple.com/documentation/eventkit)
* [EventKitUI](https://developer.apple.com/documentation/eventkitui)
* [FileProvider](https://developer.apple.com/documentation/fileprovider)
* [FileProviderUI](https://developer.apple.com/documentation/fileproviderui)
* [HealthKit](https://developer.apple.com/documentation/healthkit)
* [HomeKit](https://developer.apple.com/documentation/homekit)
* [iAd](https://developer.apple.com/documentation/iad)
* [JavaScriptCore](https://developer.apple.com/documentation/javascriptcore)
* [MapKit](https://developer.apple.com/documentation/mapkit)
* [Messages](https://developer.apple.com/documentation/messages)
* [MessageUI](https://developer.apple.com/documentation/messageui)
* [MultipeerConnectivity](https://developer.apple.com/documentation/multipeerconnectivity)
* [Natural Language](https://developer.apple.com/documentation/naturallanguage)
* [NewsstandKit](https://developer.apple.com/documentation/newsstandkit)
* [NotificationCenter](https://developer.apple.com/documentation/notificationcenter)
* [PassKit](https://developer.apple.com/documentation/passkit)
* [PreferencePanes](https://developer.apple.com/documentation/preferencepanes)
* [PushKit](https://developer.apple.com/documentation/pushkit)
* [QuickLook](https://developer.apple.com/documentation/quicklook)
* [QuickLookThumbnailing](https://developer.apple.com/documentation/quicklookthumbnailing) Beta
* [SafariServices](https://developer.apple.com/documentation/safariservices)
* [SiriKit](https://developer.apple.com/documentation/sirikit)
* [SMS and Call Reporting](https://developer.apple.com/documentation/sms_and_call_reporting)
* [Social](https://developer.apple.com/documentation/social)
* [Speech](https://developer.apple.com/documentation/speech)
* [StoreKit](https://developer.apple.com/documentation/storekit)
* [TVServices](https://developer.apple.com/documentation/tvservices)
* [UserNotifications](https://developer.apple.com/documentation/usernotifications)
* [UserNotificationsUI](https://developer.apple.com/documentation/usernotificationsui)
* [WatchConnectivity](https://developer.apple.com/documentation/watchconnectivity)
* [WebKit](https://developer.apple.com/documentation/webkit)

### Media

* [Apple News](https://developer.apple.com/documentation/apple_news)
* [AssetsLibrary](https://developer.apple.com/documentation/assetslibrary)
* [AudioToolbox](https://developer.apple.com/documentation/audiotoolbox)
* [AudioUnit](https://developer.apple.com/documentation/audiounit)
* [AVFoundation](https://developer.apple.com/documentation/avfoundation)
* [AVKit](https://developer.apple.com/documentation/avkit)
* [Core Audio](https://developer.apple.com/documentation/coreaudio)
* [Core Audio Kit](https://developer.apple.com/documentation/coreaudiokit)
* [Core Audio Types](https://developer.apple.com/documentation/coreaudiotypes) Beta
* [Core Haptics](https://developer.apple.com/documentation/corehaptics) Beta
* [Core Media](https://developer.apple.com/documentation/coremedia)
* [Core MIDI](https://developer.apple.com/documentation/coremidi)
* [Core Video](https://developer.apple.com/documentation/corevideo)
* [FxPlug](https://developer.apple.com/documentation/fxplug)
* [HTTP Live Streaming](https://developer.apple.com/documentation/http_live_streaming)
* [iTunesLibrary](https://developer.apple.com/documentation/ituneslibrary)
* [Media Player](https://developer.apple.com/documentation/mediaplayer)
* [MediaAccessibility](https://developer.apple.com/documentation/mediaaccessibility)
* [MediaLibrary](https://developer.apple.com/documentation/medialibrary)
* [PhotoKit](https://developer.apple.com/documentation/photokit)
* [Professional Video Applications](https://developer.apple.com/documentation/professional_video_applications)
* [QTKit](https://developer.apple.com/documentation/qtkit)
* [ScreenSaver](https://developer.apple.com/documentation/screensaver)
* [SoundAnalysis](https://developer.apple.com/documentation/soundanalysis) Beta
* [VideoToolbox](https://developer.apple.com/documentation/videotoolbox)
* [VisionKit](https://developer.apple.com/documentation/visionkit) Beta

### Web

* [App Store Connect API](https://developer.apple.com/documentation/appstoreconnectapi)
* [Apple Music API](https://developer.apple.com/documentation/applemusicapi)
* [Apple Pay on the Web](https://developer.apple.com/documentation/apple_pay_on_the_web)
* [CloudKit JS](https://developer.apple.com/documentation/cloudkitjs)
* [LinkPresentation](https://developer.apple.com/documentation/linkpresentation) Beta
* [LivePhotosKit JS](https://developer.apple.com/documentation/livephotoskitjs)
* [MapKit JS](https://developer.apple.com/documentation/mapkitjs)
* [Maps Web Snapshots](https://developer.apple.com/documentation/snapshots) Beta
* [MusicKit JS](https://developer.apple.com/documentation/musickitjs)
* [Safari Extensions JS](https://developer.apple.com/documentation/safariextensions)
* [Sign In with Apple JS](https://developer.apple.com/documentation/signinwithapplejs)
* [Sign In with Apple REST API](https://developer.apple.com/documentation/signinwithapplerestapi)
* [WebKit JS](https://developer.apple.com/documentation/webkitjs)

### Developer Tools

* [Automator](https://developer.apple.com/documentation/automator)
* [Code Diagnostics](https://developer.apple.com/documentation/code_diagnostics)
* [InstallerJS](https://developer.apple.com/documentation/installerjs)
* [Playground Support](https://developer.apple.com/documentation/playgroundsupport)
* [PlaygroundBluetooth](https://developer.apple.com/documentation/playgroundbluetooth)
* [ScriptingBridge](https://developer.apple.com/documentation/scriptingbridge)
* [Swift Packages](https://developer.apple.com/documentation/swift_packages)
* [Swift Playgrounds](https://developer.apple.com/documentation/swift_playgrounds)
* [XcodeKit](https://developer.apple.com/documentation/xcodekit)
* [XCTest](https://developer.apple.com/documentation/xctest)

### System

* [Accelerate](https://developer.apple.com/documentation/accelerate)
* [Apple CryptoKit](https://developer.apple.com/documentation/cryptokit) Beta
* [AuthenticationServices](https://developer.apple.com/documentation/authenticationservices)
* [CFNetwork](https://developer.apple.com/documentation/cfnetwork)
* [Collaboration](https://developer.apple.com/documentation/collaboration)
* [Compression](https://developer.apple.com/documentation/compression)
* [Core Bluetooth](https://developer.apple.com/documentation/corebluetooth)
* [Core NFC](https://developer.apple.com/documentation/corenfc)
* [Core Services](https://developer.apple.com/documentation/coreservices)
* [Core Telephony](https://developer.apple.com/documentation/coretelephony)
* [Core WLAN](https://developer.apple.com/documentation/corewlan)
* [CryptoTokenKit](https://developer.apple.com/documentation/cryptotokenkit)
* [DarwinNotify](https://developer.apple.com/documentation/darwinnotify)
* [Device Management](https://developer.apple.com/documentation/devicemanagement)
* [DiskArbitration](https://developer.apple.com/documentation/diskarbitration)
* [Dispatch](https://developer.apple.com/documentation/dispatch)
* [dnssd](https://developer.apple.com/documentation/dnssd)
* [DriverKit](https://developer.apple.com/documentation/driverkit) Beta
* [EndpointSecurity](https://developer.apple.com/documentation/endpointsecurity) Beta
* [ExceptionHandling](https://developer.apple.com/documentation/exceptionhandling)
* [ExecutionPolicy](https://developer.apple.com/documentation/executionpolicy) Beta
* [ExternalAccessory](https://developer.apple.com/documentation/externalaccessory)
* [FinderSync](https://developer.apple.com/documentation/findersync)
* [ForceFeedback](https://developer.apple.com/documentation/forcefeedback)
* [FWAUserLib](https://developer.apple.com/documentation/fwauserlib)
* [GSS](https://developer.apple.com/documentation/gss)
* [HIDDriverKit](https://developer.apple.com/documentation/hiddriverkit)Beta
* [Hypervisor](https://developer.apple.com/documentation/hypervisor)
* [InputMethodKit](https://developer.apple.com/documentation/inputmethodkit)
* [IOBluetooth](https://developer.apple.com/documentation/iobluetooth)
* [IOBluetoothUI](https://developer.apple.com/documentation/iobluetoothui)
* [IOKit](https://developer.apple.com/documentation/iokit)
* [IOSurface](https://developer.apple.com/documentation/iosurface)
* [IOUSBHost](https://developer.apple.com/documentation/iousbhost)Beta
* [Kernel](https://developer.apple.com/documentation/kernel)
* [LatentSemanticMapping](https://developer.apple.com/documentation/latentsemanticmapping)
* [LocalAuthentication](https://developer.apple.com/documentation/localauthentication)
* [Logging](https://developer.apple.com/documentation/logging)Beta
* [MetricKit](https://developer.apple.com/documentation/metrickit)Beta
* [MobileCoreServices](https://developer.apple.com/documentation/mobilecoreservices)
* [Network](https://developer.apple.com/documentation/network)
* [NetworkExtension](https://developer.apple.com/documentation/networkextension)
* [NetworkingDriverKit](https://developer.apple.com/documentation/networkingdriverkit)Beta
* [Objective-C Runtime](https://developer.apple.com/documentation/objectivec)
* [OpenDirectory](https://developer.apple.com/documentation/opendirectory)
* [os](https://developer.apple.com/documentation/os)
* [Security](https://developer.apple.com/documentation/security)
* [SecurityFoundation](https://developer.apple.com/documentation/securityfoundation)
* [SecurityInterface](https://developer.apple.com/documentation/securityinterface)
* [ServiceManagement](https://developer.apple.com/documentation/servicemanagement)
* [simd](https://developer.apple.com/documentation/simd)
* [SystemConfiguration](https://developer.apple.com/documentation/systemconfiguration)
* [SystemExtensions](https://developer.apple.com/documentation/systemextensions)Beta
* [USBDriverKit](https://developer.apple.com/documentation/usbdriverkit)Beta
* [vmnet](https://developer.apple.com/documentation/vmnet)
* [XPC](https://developer.apple.com/documentation/xpc)



## Reference

[IOS架构师之路：我对IOS架构的点点认识\(大纲\)](https://blog.csdn.net/u014011807/article/details/46830585)

