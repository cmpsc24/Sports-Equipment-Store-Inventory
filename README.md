class Item:
    def __init__(self, item_id, name, category, quantity, size=None, color=None, expiration_date=None):
        self.item_id = item_id
        self.name = name
        self.category = category
        self.quantity = quantity
        self.size = size
        self.color = color
        self.expiration_date = expiration_date

    def update_quantity(self, amount):
        self.quantity += amount

    def get_details(self):
        return {
            "ID": self.item_id,
            "Name": self.name,
            "Category": self.category,
            "Quantity": self.quantity,
            "Size": self.size,
            "Color": self.color,
            "Expiration Date": self.expiration_date,
        }
class Inventory:
    def __init__(self):
        self.items = []

    def add_item(self, item: Item):
        self.items.append(item)

    def remove_item(self, item_id: int):
        self.items = [item for item in self.items if item.item_id != item_id]

    def get_item(self, item_id: int) -> Item:
        for item in self.items:
            if item.item_id == item_id:
                return item
        return None

    def list_items(self) -> list:
        return [item.get_details() for item in self.items]

    def update_inventory(self, item_id: int, amount: int):
        item = self.get_item(item_id)
        if item:
            item.update_quantity(amount)

    def check_stock(self, item_id: int) -> int:
        item = self.get_item(item_id)
        return item.quantity if item else 0
class Report:
    def __init__(self, inventory: Inventory):
        self.inventory = inventory

    def low_stock_report(self, threshold: int) -> list:
        return [item.get_details() for item in self.inventory.items if item.quantity < threshold]

    def expiry_report(self) -> list:
        from datetime import datetime
        return [item.get_details() for item in self.inventory.items if item.expiration_date and item.expiration_date < datetime.now()]

    def sales_report(self) -> dict:
        # Placeholder for sales data integration
        pass

    def generate_report(self) -> dict:
        return {
            "Total Items": len(self.inventory.items),
            "Items in Stock": sum(item.quantity for item in self.inventory.items),
            "Low Stock Items": self.low_stock_report(threshold=5),  # Example threshold
            "Expiring Items": self.expiry_report(),
        }
class User:
    def __init__(self, username: str, role: str):
        self.username = username
        self.role = role

    def login(self):
        # Implement login logic (e.g., checking username and password)
        pass

    def get_permissions(self) -> list:
        permissions = {
            "admin": ["add", "remove", "update", "view", "generate_report"],
            "manager": ["view", "generate_report"],
        }
        return permissions.get(self.role, [])

    def perform_inventory_actions(self, action: str, *args):
        if action in self.get_permissions():
            # Implement action logic, e.g., self.inventory.add_item(args[0])
            pass
        else:
            print("Permission denied for this action.")
import json

class Inventory:
    # ... (existing code)

    def save_to_file(self, filename: str):
        with open(filename, 'w') as f:
            json.dump([item.get_details() for item in self.items], f)

    def load_from_file(self, filename: str):
        with open(filename, 'r') as f:
            items_data = json.load(f)
            for item_data in items_data:
                item = Item(**item_data)
                self.add_item(item)
# Import necessary libraries
from datetime import datetime, timedelta

# Create an instance of Inventory
inventory = Inventory()

# Create some items
item1 = Item(item_id=1, name="Basketball", category="Sports", quantity=10, size="Medium", color="Orange")
item2 = Item(item_id=2, name="Tennis Racket", category="Sports", quantity=5, size="Standard", color="Red")
item3 = Item(item_id=3, name="Soccer Ball", category="Sports", quantity=15, size="Standard", color="White")
item4 = Item(item_id=4, name="Baseball Glove", category="Sports", quantity=2, size="Large", color="Brown", expiration_date=datetime.now() + timedelta(days=30))  # Example of item with an expiration date

# Add items to the inventory
inventory.add_item(item1)
inventory.add_item(item2)
inventory.add_item(item3)
inventory.add_item(item4)

# List all items in the inventory
print("Current Inventory:")
for item in inventory.list_items():
    print(item)

# Update the quantity of an item
inventory.update_inventory(item_id=1, amount=-2)  # Remove 2 Basketballs
inventory.update_inventory(item_id=2, amount=5)   # Add 5 Tennis Rackets

# Check stock of a specific item
print(f"\nStock of Tennis Racket: {inventory.check_stock(item_id=2)}")

# Generate a low stock report
report = Report(inventory)
print("\nLow Stock Report (Threshold = 5):")
low_stock_items = report.low_stock_report(threshold=5)
for item in low_stock_items:
    print(item)

# Generate an expiry report
print("\nExpiry Report:")
expiry_items = report.expiry_report()
for item in expiry_items:
    print(item)

# Generate a summary report
print("\nInventory Summary Report:")
summary_report = report.generate_report()
print(summary_report)

# Save inventory to a JSON file
inventory.save_to_file("inventory_data.json")

# Load inventory from a JSON file (optional, here we just demonstrate usage)
new_inventory = Inventory()
new_inventory.load_from_file("inventory_data.json")
print("\nLoaded Inventory from File:")
for item in new_inventory.list_items():
    print(item)

# User actions demonstration
user = User(username="admin", role="admin")

# Simulating user performing inventory actions
if "add" in user.get_permissions():
    print("\nUser has permission to add items.")
    new_item = Item(item_id=5, name="Football", category="Sports", quantity=20, size="Standard", color="Black")
    inventory.add_item(new_item)

print("\nUpdated Inventory after adding Football:")
for item in inventory.list_items():
    print(item)
