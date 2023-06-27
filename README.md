# tuist_app_example

https://www.youtube.com/watch?v=1inP-Y118p8&list=PLB8RAOcCIoizZAF3_YPGhpjIf0mA0RqB5


# Commands

```shell 
tuist init --platform ios
tuist generate
tuist edit
```

# Basic Configuration, Project.swift

- You have to manually create config files according to your configuration.
  - Debug.xcconfig
  - Release.xcconfig

```swift
import ProjectDescription

private let bundleID: String = "com.paigesoftware.tuistapp"
private let version: String = "0.0.1"
private let bundleVersion: String = "1"
private let iOSTargetVersion: String = "16.0"

private let basePath: String = "Targets/tuist-app"
private let appName: String = "Tuist-App"

let project = Project(
    name: appName,
    packages: [],
    settings: Settings.settings(configurations: makeConfiguration()),
    targets: [
        Target(
            name: "iOS",
            platform: .iOS,
            product: .app,
            bundleId: bundleID,
            deploymentTarget: .iOS(targetVersion: iOSTargetVersion, devices: .iphone),
            infoPlist: makeInfoPlist(),
            sources: ["\(basePath)/Sources/**"],
            resources: "\(basePath)/Resources/**",
            settings: baseSettings()
        )
    ],
    additionalFiles: ["README.md"])

/// Create extneded plist for iOS
/// - Returns: InfoPlist
private func makeInfoPlist(merging other: [String: InfoPlist.Value] = [:]) -> InfoPlist {
    var extendedPlist: [String: InfoPlist.Value] = [
        "UIApplicationSceneManifest": ["UIApplicationSupportsMultipleScenes": true],
        "UILaunchScreen": [],
        "UISupportedInterfaceOrientations": [
            "UIInterfaceOrientationPortrait",
        ],
        "CFBundleShortVersionString": "\(version)",
        "CFBundleVersion": "\(bundleVersion)",
        "CFBundleDisplayName": "\(appName)"
    ]
    
    other.forEach { (key: String, value: InfoPlist.Value) in
        extendedPlist[key] = value
    }
    
    return InfoPlist.extendingDefault(with: extendedPlist)
}

/// Create dev and release configuration
/// - Returns: Configuration Tuple
private func makeConfiguration() -> [Configuration] {
    let debug: Configuration = Configuration.debug(name: "Debug", xcconfig: "Configs/Debug.xcconfig")
    let release: Configuration = Configuration.debug(name: "Release", xcconfig: "Configs/Release.xcconfig")
    return [debug, release]
}

private func baseSettings() -> Settings {
    var settings = SettingsDictionary()
    return Settings.settings(
        base: settings,
        configurations: [],
        defaultSettings: .recommended
    )
}


```
