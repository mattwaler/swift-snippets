# Previews

How to setup preview data for use in your app previews!

```swift
import SwiftUI
import SwiftData

// Container for previews
let previewContainer = {
  do {
    let container = try ModelContainer(for: [Settings.self], configurations: ModelConfiguration(isStoredInMemoryOnly: true))
    Task { @MainActor in
      let context = container.mainContext
      context.insert(Settings())
      // Populate your container with logic here!
    }
    return container
  } catch {
    fatalError("Failed to create container: \(error.localizedDescription)")
  }
}

// Modifier to add to previews in view files
struct PreviewModifier: ViewModifier {
  func body(content: Content) -> some View {
    NavigationStack {
      content
    }
    .modelContainer(previewContainer())
  }
}

// How to use the preview in other files
#Preview {
  struct Preview: View {
    @State var test = "Hello there!"
		var body: some View {
      Text(test)
    }
  }
  return Preview().modifier(PreviewModifier())
}
