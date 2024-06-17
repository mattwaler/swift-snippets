# Notifier

A starting point for creating a Notifier class to configure push notifications and badge counts in a Swift app.

```swift
import SwiftUI
import SwiftData

struct Notifier {
	func requestAuth() {
		UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { success, error in
			if let error = error {
				print(error.localizedDescription)
			}
		}
	}

	func resetBadgeCount() -> Void {
		UNUserNotificationCenter.current().setBadgeCount(0)
	}

	func setBadgeCount(count: Int) -> Void {
		UNUserNotificationCenter.current().setBadgeCount(count)
	}

	func removeNotifications(uid: String) {
		UNUserNotificationCenter.current().removePendingNotificationRequests(withIdentifiers: [uid])
	}

	func scheduleNotification(uid: String, name: String, time: Date) {
		self.requestAuth()
		self.removeNotifications(uid: uid)

		// Construct New Notification
		let content = UNMutableNotificationContent()
		content.title = name
		content.subtitle = ""
		content.sound = .default

		// Construct Notification Date
		let dateComponents = Calendar.current.dateComponents(
			[.year,.day,.month,.hour,.minute,.second],
			from: time
		)

		// Create Trigger & Request
		let trigger = UNCalendarNotificationTrigger(dateMatching: dateComponents, repeats: false)
		let request = UNNotificationRequest(
			identifier: uid,
			content: content,
			trigger: trigger
		)

		// Schedule Notification!
		UNUserNotificationCenter.current().add(request) { error in
      if let error = error {
				print(error.localizedDescription)
			}
		}
	}
}
```
