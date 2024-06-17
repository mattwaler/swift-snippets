# Purchaser

An example class to handle app store purchases!

```swift
import SwiftUI
import StoreKit

@Observable class Purchaser {
	var products: [Product] = []
	var activeTransactions: Set<StoreKit.Transaction> = []
	private var updates: Task<Void, Never>?

	init() {
		updates = Task {
			for await update in StoreKit.Transaction.updates {
				if let transaction = try? update.payloadValue {
					await fetchActiveTransactions()
					await transaction.finish()
				}
			}
		}
	}

	deinit {
		updates?.cancel()
	}

	func restorePurchases() async {
		do {
			try await StoreKit.AppStore.sync()
			await fetchActiveTransactions()
		} catch {
			print("Error syncing past purchases.")
		}
	}

	func fetchProducts() async {
		do {
			products = try await Product.products(for: ["agone.lifetime.premium"])
		} catch {
			print("Error fetching products")
		}
	}

	func fetchActiveTransactions() async {
		var activeTransactions: Set<StoreKit.Transaction> = []
		for await entitlement in StoreKit.Transaction.currentEntitlements {
			if let transaction = try? entitlement.payloadValue {
				activeTransactions.insert(transaction)
			}
		}
		self.activeTransactions = activeTransactions
	}

	func purchase(_ product: Product) async throws {
		let result = try await product.purchase()
		switch result {
		case .success(let verificationResult):
			if let transaction = try? verificationResult.payloadValue {
				activeTransactions.insert(transaction)
				await transaction.finish()
			}
		case .userCancelled:
			break
		case .pending:
			break
		@unknown default:
			break
		}
	}
}

```
