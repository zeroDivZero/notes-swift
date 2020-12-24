# SWIFT PACKAGE MANAGER

## Add Package Dependency

In Xcode: **File > Swift Packages > Add Package Dependency**

Enter repo URL. Or with GitHub added to **Accounts**, can type name of package to see list of matching repos.

Alternatively, navigate to target settings' **Swift Packages** pane to add/remove packages.

## Decide Requirements

Tells Xcode how to update package.

### Version

If project accepts updates up to next major or minor version. Or specify version range or exact version. Requires package to conform to semantic versioning.

### Branch

Select branch name of package to follow. Useful if developing package and don't want latest published yet.

### Commit

Select commit hash of package. Generally not recommended. Ensures dependency doesn't change, but also not getting any update.

## Update

In Xcode: **File > Swift Packages**

There are options like updating to latest and resolving versions.

## Usage

Import package's product as module. See package's documentation for usage details.

## Coordinate Package Versions Across Team

When package added to project, Xcode creates `Package.resolved`. It lists Git commits to which each package resolves and checksums of binary dependencies. Commit this file in Git to ensure that everyone is using same versions of packages.

Can find `Package.resolved` inside `.xcodeproj` directory at `[appName].xcodeproj/project.workspace/xcshareddata/swiftpm/Package.resolved`.

## Binary Dependencies

Prefer source-based dependency over binary. Can inspect code, debug more easily, and recompile. Binary dependency can only support platforms its included binaries support, and only available for Apple platforms.

To find out if dependency is binary or depends on one:

1. In project navigator, under **Swift Package Dependencies** expand individual package.
2. Look for folder called **Referenced Binaries**. If it exists, package distributes binary or has binary dependency.
3. To further inspect referenced binary, ctrl-click **XCFramework** bundle inside **Referenced Binaries** folder, and open it in **Finder**.

Binary dependency author must create checksum and include it in package manifest. When Xcode resolves or updates dependencies, it doesn't allow binary dependency to change checksum without also changing version.

## Command Line

To see help of commands:

```zsh
% swift package --help
```
