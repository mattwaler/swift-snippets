# Migrations

How to setup and enable the ability to do migrations with SwiftData.

```swift
import SwiftData

// Construct schema version
enum SchemaV1: VersionedSchema {
  static var schema = Schema([Settings.self])
	static var versionIdentifier = Schema.Version(1, 0, 0)
  static var models: [any PersistentModel.Type] {
		[SettingsV1.self]
  }
}

// Construct migration plan
enum MigrationPlan: SchemaMigrationPlan {
	static var schemas: [any VersionedSchema.Type] {
		[SchemaV1.self]
	}
	static var stages: [MigrationStage] {
		[]
	}
}

// Construct versioned model container
var versionedModelContainer = {
  let schema = SchemaV1.schema
  let modelConfiguration = ModelConfiguration(schema: schema, isStoredInMemoryOnly: false)
  do {
    let container = try ModelContainer(
      for: schema,
      migrationPlan: MigrationPlan.self,
      configurations: [modelConfiguration]
    )
    return container
  } catch {
    fatalError("Could not create ModelContainer: \(error)")
  }
}

// Usage in app
var body: some Scene {
  WindowGroup {
    ContentView()
  }
  .modelContainer(versionedModelContainer())
}
```
