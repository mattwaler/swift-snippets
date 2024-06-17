# SwiftData Model

An example for how to structure your SwiftData models to be compatible with migrations and previews!

```swift
import SwiftUI
import SwiftData

// Create a typealias so that when you migrate your model, you can update in one place!
typealias Settings = SettingsV1

@Model
class SettingsV1 {
	var showAppBadges = true
  init() {}
}
```
