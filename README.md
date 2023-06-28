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
private let swiftPackagePath: String = "SwiftPackages"
private let appName: String = "Tuist-App"

let project = Project(
    name: appName,
    packages: [
        .package(path: "\(swiftPackagePath)/Onboarding"), // add new modules with SPM
    ],
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
            dependencies: [
                .package(product: "Onboarding"), // add dependency (for modules)
            ],
            settings: baseSettings()
        ),
    ],
    additionalFiles: ["README.md"]
)

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
        "CFBundleDisplayName": "$(APP_DISPLAY_NAME)", // xcconfig.
    ]

    other.forEach { (key: String, value: InfoPlist.Value) in
        extendedPlist[key] = value
    }

    return InfoPlist.extendingDefault(with: extendedPlist)
}

/// Create dev and release configuration
/// - Returns: Configuration Tuple
private func makeConfiguration() -> [Configuration] {
    let debug = Configuration.debug(name: "Debug", xcconfig: "Configs/Debug.xcconfig")
    let release = Configuration.debug(name: "Release", xcconfig: "Configs/Release.xcconfig")
    return [debug, release]
}

private func baseSettings() -> Settings {
    let msForWarning = 5
    let settings = SettingsDictionary().otherSwiftFlags(
        "-Xfrontend -warn-long-expression-type-checking=\(msForWarning)",
        "-Xfrontend -warn-long-function-bodies=\(msForWarning)"
    )
    return Settings.settings(
        base: settings,
        configurations: [],
        defaultSettings: .recommended
    )
}
```

# Onboarding SPM

```swift
// swift-tools-version: 5.8
// The swift-tools-version declares the minimum version of Swift required to build this package.

import PackageDescription

let package = Package(
    name: "Onboarding",
    defaultLocalization: "en",
    platforms: [.iOS(.v16)],
    products: [
        // Products define the executables and libraries a package produces, and make them visible to other packages.
        .library(
            name: "Onboarding",
            targets: ["OnboardingUI"]
        ),
    ],
    dependencies: [
        .package(name: "UIComponents", path: "../UIComponents"),
        .package(url: "https://github.com/realm/SwiftLint", exact: .init(0, 51, 0)),
        // Dependencies declare other packages that this package depends on.
        // .package(url: /* package url */, from: "1.0.0"),
    ],
    targets: [
        // Targets are the basic building blocks of a package. A target can define a module or a test suite.
        // Targets can depend on other targets in this package, and on products in packages this package depends on.
        .target(
            name: "OnboardingUI",
            dependencies: [
                "UIComponents",
                "OnboardingDomain"
            ],
            resources: [
                .process("Resources/LottieFiles/coach.json"),
            ],
            plugins: [
//                .plugin(name: "SwiftLintPlugin", package: "SwiftLint")
            ]
        ),
        .target(
            name: "OnboardingData",
            dependencies: [],
            plugins: [
//                .plugin(name: "SwiftLintPlugin", package: "SwiftLint")
            ]
        ),
        .target(
            name: "OnboardingDomain",
            dependencies: [
                "OnboardingData"
            ],
            plugins: [
//                .plugin(name: "SwiftLintPlugin", package: "SwiftLint")
            ]
        ),
        .testTarget(
            name: "OnboardingUITests",
            dependencies: ["OnboardingUI"]
        ),
        .testTarget(
            name: "OnboardingDataTests",
            dependencies: [],
            plugins: [
                .plugin(name: "SwiftLintPlugin", package: "SwiftLint")
            ]
        ),
        .testTarget(
            name: "OnboardingDomainTests",
            dependencies: [],
            plugins: [
                .plugin(name: "SwiftLintPlugin", package: "SwiftLint")
            ]
        ),
    ]
)

```

# UIComponents SPM

```swift
// swift-tools-version: 5.8
// The swift-tools-version declares the minimum version of Swift required to build this package.

import PackageDescription

let package = Package(
    name: "UIComponents",
    platforms: [.iOS(.v16)],
    products: [
        // Products define the executables and libraries a package produces, and make them visible to other packages.
        .library(
            name: "UIComponents",
            targets: ["UIComponents"]
        ),
    ],
    dependencies: [
        // Dependencies declare other packages that this package depends on.
        // .package(url: /* package url */, from: "1.0.0"),
        .package(url: "https://github.com/airbnb/lottie-spm.git", exact: "4.1.3"),
    ],
    targets: [
        // Targets are the basic building blocks of a package. A target can define a module or a test suite.
        // Targets can depend on other targets in this package, and on products in packages this package depends on.
        .target(
            name: "UIComponents",
            dependencies: [
                .product(name: "Lottie", package: "lottie-spm"),
            ]
        ),
        .testTarget(
            name: "UIComponentsTests",
            dependencies: ["UIComponents"]
        ),
    ]
)

```
