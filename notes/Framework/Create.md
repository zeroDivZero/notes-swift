# CREATE

Xcode: **File > New > Project > Framework**

Entities and members should be declared `public` or `open` if part of public API (accessible outside of module).

Build. Should generate `.framework` file in folder `Products`.

In app that needs to use framework, select project in **Project Navigator > General > Frameworks, Libraries, and Embedded Content**, drag `.framework` there. If framework only used for this app, can add framework's project under app's project as subproject for easier editing. If want to rebuild framework automatically each time app is built, add framework as dependency in **Build Phases**.

## Storyboard

If framework contains component used in storyboard, make sure for that component under **Identity inspector > Custom Class**, correct module is selected, or get runtime error.

### Live View

To let framework client change properties of custom control visually, need to use **Live View**: appears in IB as it does in app. Add `@IBDesignable` in front of entity.

```swift
@IBDesignable public class Knob: UIControl {
```

Then implement prepareForInterfaceBuilder(). Only called when rendering in IB.

```swift
extension Knob {
  public override func prepareForInterfaceBuilder() {
    super.prepareForInterfaceBuilder()

    renderer.updateBounds(bounds)
  }
}
```

(Xcode: **Editor > Automatically Refresh Views** should be checked)

Annotate properties with `@IBInspectable` so they are configurable in IB's **Attributes inspector**.
