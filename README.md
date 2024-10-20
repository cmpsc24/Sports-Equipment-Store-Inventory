class Item:
    def __init__(self, item_id, name, category, size=None, color=None, expiration_date=None, quantity=0):
        self.item_id = item_id  # Unique identifier for the item
        self.name = name  # Name of the item
        self.category = category  # Category of the item (e.g., balls, rackets)
        self.size = size  # Size of the item, if applicable
        self.color = color  # Color of the item, if applicable
        self.expiration_date = expiration_date  # Expiration date for perishable items
        self.quantity = quantity  # Number of items in stock

    def update_quantity(self, amount):
        """Update the quantity of the item."""
        self.quantity += amount

    def get_details(self):
        """Return a string representation of the item details."""
        return (f"ID: {self.item_id}, Name: {self.name}, Category: {self.category}, "
                f"Size: {self.size}, Color: {self.color}, Expiry: {self.expiration_date}, "
                f"Quantity: {self.quantity}")
class Inventory:
    def __init__(self):
        self.items = []  # List to store Item objects

    def add_item(self, item):
        """Add an item to the inventory."""
        self.items.append(item)

    def remove_item(self, item_id):
        """Remove an item from the inventory by ID."""
        self.items = [item for item in self.items if item.item_id != item_id]

    def get_item(self, item_id):
        """Retrieve an item from the inventory by ID."""
        for item in self.items:
            if item.item_id == item_id:
                return item
        return None

    def list_items(self):
        """List all items in the inventory."""
        return [item.get_details() for item in self.items]

    def update_inventory(self, item_id, amount):
        """Update the inventory quantity for a specific item."""
        item = self.get_item(item_id)
        if item:
            item.update_quantity(amount)

    def check_stock(self, item_id):
        """Check stock of a specific item."""
        item = self.get_item(item_id)
        return item.quantity if item else 0
class Report:
    def __init__(self, inventory):
        self.inventory = inventory

    def low_stock_report(self, threshold=5):
        """Generate a report for items below a certain stock threshold."""
        return [item.get_details() for item in self.inventory.items if item.quantity < threshold]

    def expiry_report(self):
        """Generate a report for items that are expired or near expiry."""
        from datetime import datetime
        current_date = datetime.now()
        return [item.get_details() for item in self.inventory.items if item.expiration_date and item.expiration_date < current_date]

    def sales_report(self):
        """Generate a generic sales report (placeholder for future functionality)."""
        # Placeholder for sales logic
        return "Sales report generation not yet implemented."

    def generate_report(self):
        """Generate a comprehensive report."""
        return {
            "low_stock": self.low_stock_report(),
            "expiry_items": self.expiry_report()
        }
class User:
    def __init__(self, username, role):
        self.username = username  # Username of the user
        self.role = role  # Role of the user (admin, manager)

    def login(self):
        """Simulate a user login."""
        return f"{self.username} logged in as {self.role}."

    def get_permissions(self):
        """Get permissions based on user role."""
        permissions = {
            "admin": ["add_item", "remove_item", "update_inventory", "generate_reports"],
            "manager": ["add_item", "update_inventory"]
        }
        return permissions.get(self.role, [])

    def perform_inventory_actions(self, action, *args):
        """Perform inventory actions based on user role."""
        # This method would contain logic to perform actions allowed for the user
        # (e.g., call inventory methods based on permissions)
        pass
import json

class InventoryManager:
    def __init__(self, inventory):
        self.inventory = inventory

    def save_to_file(self, filename):
        """Save inventory data to a JSON file."""
        with open(filename, 'w') as f:
            json.dump([item.__dict__ for item in self.inventory.items], f)

    def load_from_file(self, filename):
        """Load inventory data from a JSON file."""
        with open(filename, 'r') as f:
            items_data = json.load(f)
            self.inventory.items = [Item(**item) for item in items_data]
# Create inventory and report instances
inventory = Inventory()
report = Report(inventory)

# Create user and login
user = User(username="admin", role="admin")
print(user.login())

# Adding items
item1 = Item(item_id=1, name="Tennis Ball", category="Balls", size="Medium", color="Yellow", quantity=10)
inventory.add_item(item1)

# Listing items
print(inventory.list_items())

# Generating reports
print(report.low_stock_report())
