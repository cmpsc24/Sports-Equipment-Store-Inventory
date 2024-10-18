class Item:
    def __init__(self, item_id, name, category, size=None, color=None, expiration_date=None):
        self.item_id = item_id
        self.name = name
        self.category = category
        self.size = size
        self.color = color
        self.expiration_date = expiration_date
        self.quantity = 0

    def update_quantity(self, amount):
        """Updates the quantity of the item."""
        self.quantity += amount

    def get_details(self):
        """Returns a dictionary of item details."""
        return {
            'ID': self.item_id,
            'Name': self.name,
            'Category': self.category,
            'Size': self.size,
            'Color': self.color,
            'Expiration Date': self.expiration_date,
            'Quantity': self.quantity
        }
class Inventory:
    def __init__(self):
        self.items = {}

    def add_item(self, item):
        """Adds a new item to the inventory."""
        self.items[item.item_id] = item

    def remove_item(self, item_id):
        """Removes an item from the inventory by ID."""
        if item_id in self.items:
            del self.items[item_id]

    def get_item(self, item_id):
        """Returns an item by its ID."""
        return self.items.get(item_id)

    def list_items(self):
        """Returns a list of all items in the inventory."""
        return [item.get_details() for item in self.items.values()]

    def update_inventory(self, item_id, quantity_change):
        """Updates the quantity of an item."""
        item = self.get_item(item_id)
        if item:
            item.update_quantity(quantity_change)

    def check_stock(self, item_id):
        """Checks the stock level of a specific item."""
        item = self.get_item(item_id)
        return item.quantity if item else None
class Report:
    def __init__(self, inventory):
        self.inventory = inventory

    def low_stock_report(self, threshold=5):
        """Generates a report of items below a certain stock threshold."""
        return [item.get_details() for item in self.inventory.items.values() if item.quantity < threshold]

    def expiry_report(self, current_date):
        """Generates a report of items that are expired or close to expiration."""
        return [item.get_details() for item in self.inventory.items.values() if item.expiration_date and item.expiration_date < current_date]

    def sales_report(self):
        """Generates a general sales report (dummy example)."""
        # This can be enhanced to reflect actual sales data.
        return [{"item_id": item.item_id, "quantity": item.quantity} for item in self.inventory.items.values()]

    def generate_report(self):
        """Generates a summary report of the inventory."""
        return {
            "total_items": len(self.inventory.items),
            "items": self.inventory.list_items()
        }
class User:
    def __init__(self, username, role):
        self.username = username
        self.role = role

    def login(self):
        """Handles user login (placeholder for actual login logic)."""
        return f"{self.username} logged in as {self.role}"

    def get_permissions(self):
        """Returns user permissions based on role."""
        permissions = {
            "admin": ["add", "remove", "update", "view", "report"],
            "manager": ["view", "report"]
        }
        return permissions.get(self.role, [])

    def perform_inventory_actions(self, action, inventory, item=None):
        """Performs inventory actions based on user role."""
        if action == "add" and "add" in self.get_permissions():
            if item:
                inventory.add_item(item)
        elif action == "remove" and "remove" in self.get_permissions():
            if item:
                inventory.remove_item(item.item_id)
        elif action == "update" and "update" in self.get_permissions():
            if item:
                inventory.update_inventory(item.item_id, item.quantity)
        elif action == "view" and "view" in self.get_permissions():
            return inventory.list_items()
import json

class InventoryPersistence:
    @staticmethod
    def save_to_file(inventory, filename='inventory.json'):
        """Saves the inventory to a JSON file."""
        with open(filename, 'w') as file:
            json.dump([item.get_details() for item in inventory.items.values()], file)

    @staticmethod
    def load_from_file(inventory, filename='inventory.json'):
        """Loads inventory from a JSON file."""
        try:
            with open(filename, 'r') as file:
                items = json.load(file)
                for item_data in items:
                    item = Item(**item_data)
                    inventory.add_item(item)
        except FileNotFoundError:
            print("File not found. Starting with an empty inventory.")
# Create instances
inventory = Inventory()
report = Report(inventory)

# Add items
item1 = Item(1, "Football", "Ball", size="Size 5", color="White")
item1.update_quantity(10)
inventory.add_item(item1)

item2 = Item(2, "Tennis Racket", "Racket", color="Blue")
item2.update_quantity(2)
inventory.add_item(item2)

# Generate reports
print(report.low_stock_report())
print(report.generate_report())

# User management
admin_user = User("admin_user", "admin")
print(admin_user.login())
admin_user.perform_inventory_actions("view", inventory)
