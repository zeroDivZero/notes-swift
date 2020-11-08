# LOCAL PACKAGE

When using depdendency, can't directly edit content of package. Instead, add package as local package. Don't remove dependency; adding local package overrides dependency with same name. Xcode uses package dependency again when local package removed.

To add local package:

1. Check out package's source code from its repo.
2. Open app's Xcode project or workspace.
3. Select package's folder in **Finder** and drag it into project navigator. This adds dependency's package as local package.
4. Make changes to local package.
5. When ready, push changes to package's remote repo.
6. After changes made it into package's next release, remove local package from project, and update package dependency to new version.
