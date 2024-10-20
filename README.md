import json

class Inventory:
    # existing methods...

    def save_to_file(self, filename):
        """Save inventory data to a JSON file."""
        with open(filename, 'w') as f:
            json.dump([item.get_details() for item in self.items], f)

    def load_from_file(self, filename):
        """Load inventory data from a JSON file."""
        try:
            with open(filename, 'r') as f:
                item_data = json.load(f)
                for data in item_data:
                    item = Item(**data)  # Unpacking the dictionary to create an Item
                    self.add_item(item)
        except FileNotFoundError:
            print(f"File {filename} not found.")
class Item:
    def __init__(self, item_id, name, category, size=None, color=None, expiration_date=None, quantity=0):
        self.item_id = item_id          # Unique identifier for the item
        self.name = name                # Name of the item
        self.category = category        # Category of the item (e.g., equipment, apparel)
        self.size = size                # Size attribute (if applicable)
        self.color = color              # Color attribute (if applicable)
        self.expiration_date = expiration_date  # Expiration date (if applicable)
        self.quantity = quantity        # Quantity in stock

    def update_quantity(self, amount):
        """Update the quantity of the item."""
        self.quantity += amount

    def get_details(self):
        """Return a dictionary of item details."""
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
        self.items = []  # List to hold Item objects

    def add_item(self, item):
        """Add a new item to the inventory."""
        self.items.append(item)

    def remove_item(self, item_id):
        """Remove an item from the inventory by ID."""
        self.items = [item for item in self.items if item.item_id != item_id]

    def get_item(self, item_id):
        """Retrieve an item by ID."""
        for item in self.items:
            if item.item_id == item_id:
                return item
        return None

    def list_items(self):
        """List all items in the inventory."""
        return [item.get_details() for item in self.items]

    def update_inventory(self, item_id, quantity):
        """Update the inventory for a specific item."""
        item = self.get_item(item_id)
        if item:
            item.update_quantity(quantity)

    def check_stock(self):
        """Check stock for all items and return those with low stock."""
        return [item for item in self.items if item.quantity < 5]  # Example threshold
class Report:
    def __init__(self, inventory):
        self.inventory = inventory  # Reference to Inventory object

    def low_stock_report(self):
        """Generate a report of items that are low in stock."""
        low_stock_items = self.inventory.check_stock()
        return [item.get_details() for item in low_stock_items]

    def expiry_report(self):
        """Generate a report of items nearing expiration (if applicable)."""
        from datetime import datetime
        today = datetime.now().date()
        expiring_items = [item for item in self.inventory.items if item.expiration_date and item.expiration_date <= today]
        return [item.get_details() for item in expiring_items]

    def sales_report(self):
        """Generate a sales report (this can be expanded based on your needs)."""
        # Placeholder for a more complex sales report
        return "Sales report is currently not implemented."

    def generate_report(self):
        """Generate a full report of inventory status."""
        return {
            "Total Items": len(self.inventory.items),
            "Low Stock Items": self.low_stock_report(),
            "Expiring Items": self.expiry_report(),
        }
class User:
    def __init__(self, username, role):
        self.username = username  # Username of the user
        self.role = role          # Role of the user (e.g., admin, manager)

    def login(self):
        """Simulate user login."""
        # Logic for logging in a user would go here
        return f"{self.username} logged in as {self.role}."

    def get_permissions(self):
        """Get user permissions based on role."""
        if self.role == "admin":
            return "Admin permissions: full access."
        elif self.role == "manager":
            return "Manager permissions: limited access."
        else:
            return "No permissions."

    def perform_inventory_actions(self, action, *args):
        """Perform actions on the inventory based on user role."""
        # Logic to perform actions like add, remove, or view items
        pass
