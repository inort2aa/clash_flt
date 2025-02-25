# clash_flt

A new Flutter plugin based on my [ClashKit](https://github.com/LondonX/clash-kit) and my [Tun2Socks-Android](https://github.com/LondonX/tun2socks-android)  
My `ClashKit` forked from [AppleClash/clash-apple](https://github.com/AppleClash/clash-apple), my `Tun2Socks-Android` forked from [universal-android-tun2socks](https://github.com/mokhtarabadi/universal-android-tun2socks)
👍🏻Thanks [@netyouli](https://github.com/netyouli) and [Tun2SocksKit repo](https://github.com/arror/Tun2SocksKit) for iOS tun implementation.


# Setup
### pubspec.yaml
```yaml
dependencies:
  clash_flt:
    git:
      url: https://github.com/LondonX/clash_flt.git
```
### Android
1. Requires minSdkVersion 21
2. Copy `ClashKit.aar` and `tun2socks.aar` into `<project-root>/android/app/`.
3. Add `implementation files("ClashKit.aar")` and `implementation files("tun2socks.aar")` in `<project-root>/android/app/build.gradle`'s dependencies section.

### iOS
1. Requires iOS 13 or newer. ≥15 is recommended because os iOS memory limitation of packet tunnel
2. As `gomobile` stop supporting armv7a, you need to exclude this architecture in XCode.
   * Open `Runner.xcodeproj`, select `Runner` in `PROJECT` list on the left.
   * In `Build Settings`->`All`->`Architectures`, set value to `arm64`.
   * Add `ClashKit.xcframework` into `Runner`'s `Frameworks and Libraries` an select `Do Not Embed`.
   * Add `Tun2SocksKit-main` by `Swift Package Manager (SPM)` from https://github.com/arror/Tun2SocksKit or `<project-root>/ios/Tun2SocksKit-main`.
   * Add `Tun2SocksKit` lib into `Frameworks and Libraries` of `PacketTunnel` target.
   * (Optional) Add `HevSocks5Tunnel.xcframework`
3. Modify `Runner.xcodeproj`
   * Open Runner's `Signing & Capabilities` tab.
   * You may need to enable `Network Extension` in [Apple Developer Account page](https://developer.apple.com/account/)/Certificates, IDs & Profiles/Identifiers/YOUR_BUNDLE_ID/Edit/Network Extensions checkbox, before add `Network Extension` and provision.
   * Add `Network Extension` and `Personal VPN`.
   * Check `App Proxy` and `Packet Tunnel` of `Network Extension`.
4. Add project Target of `Network-Extension`
   * Create a Target named `PacketTunnel`, the XCode will auto create a file named `PacketTunnelProvider.swift`.
   * Open `PacketTunnel`'s `Signing & Capabilities` tab.
   * Add `Network Extension` and `Personal VPN`, just as step 3.
   * Modify `PacketTunnelProvider.swift` by paste from [Example's PacketTunnelProvider.swfit](example/ios/PacketTunnel/PacketTunnelProvider.swift)
5. Add app group
   * Create a App Group named `group.<yourBundleId>` both in `Runner` and `PacketTunnel` Target.
  

# Basic usage
### Initialize
```dart
await ClashFlt.instance.init(clashHome);
```
### Download and load file of clash profile
```dart
final bool isDownloaded = await ClashFlt.instance.downloadProfile(clashProfileUrl, isForce: true);
final File? downloadedFile = await ClashFlt.instance.profileFile.value;
final bool isResolved = await ClashFlt.instance.resolveProfile();
final Profile? resolvedProfile = await ClashFlt.instance.profile.value;
```
### Get proxy groups and proxy in profile
```dart
final Profile? resolvedProfile = await ClashFlt.instance.profile.value;
final List<ProxyGroup>? groups = resolvedProfile?.proxyGroups;
final List<Proxy>? proxies = resolvedProfile?.proxies;
final List<String>? proxyNames = groups?.first.proxies;
// use ClashFlt.instance.findProxy to link proxy name with Proxy object.
```
### Selet Proxy
```dart
final ProxyGroup group;//assigned in somewhere else
final Proxy proxy;//assigned in somewhere else
if (!ClashFlt.instance.isProxySelectable(group, proxy)) return;
if(ClashFlt.instance.isProxySelected(group, proxy)) return;
ClashFlt.instance.selectProxy(group, proxy) return
```
### Start/Stop Clash VPN service
```dart
// start
final bool isStarted = await ClashFlt.instance.startClash();
// stop(no value return)
await ClashFlt.instance.stopClash();
```

# Supported APIs
## Listenables
profileFile  
profileDownloading  
countryDBFile  
countryDBDownloading  
profile  
profileResolving  
healthChecking  
state.isRunning  
## Methods
queryTrafficNow  
queryTrafficTotal  
applyConfig  
isClashRunning  
startClash  
stopClash  