import json
from datetime import datetime

class Item:
    """Represents individual items in the inventory."""
    def __init__(self, item_id, name, category, size=None, color=None, expiration_date=None, quantity=0):
        self.item_id = item_id
        self.name = name
        self.category = category
        self.size = size
        self.color = color
        self.expiration_date = expiration_date
        self.quantity = quantity

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
    """Manages a collection of Item objects."""
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
        except json.JSONDecodeError:
            print(f"Error decoding JSON from the file {filename}.")

class Report:
    """Generates reports based on the current state of the inventory."""
    def __init__(self, inventory):
        self.inventory = inventory  # Reference to Inventory object

    def low_stock_report(self):
        """Generate a report of items that are low in stock."""
        low_stock_items = self.inventory.check_stock()
        return [item.get_details() for item in low_stock_items]

    def expiry_report(self):
        """Generate a report of items nearing expiration (if applicable)."""
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
    """Handles different types of users and their permissions."""
    def __init__(self, username, role):
        self.username = username  # Username of the user
        self.role = role          # Role of the user (e.g., admin, manager)

    def login(self):
        """Simulate user login."""
        return f"{self.username} logged in as {self.role}."

    def get_permissions(self):
        """Get user permissions based on role."""
        if self.role == "admin":
            return "Admin permissions: full access."
        elif self.role == "manager":
            return "Manager permissions: limited access."
        else:
            return "No permissions."

    def perform_inventory_actions(self, inventory, action, *args):
        """Perform actions on the inventory based on user role."""
        if self.role not in ["admin", "manager"]:
            return "Insufficient permissions."
        
        if action == "add":
            item = args[0]  # Assuming item is passed
            inventory.add_item(item)
            return f"Item '{item.name}' added to inventory."
        elif action == "remove":
            item_id = args[0]
            inventory.remove_item(item_id)
            return f"Item with ID '{item_id}' removed from inventory."
        elif action == "update":
            item_id, quantity = args
            inventory.update_inventory(item_id, quantity)
            return f"Item with ID '{item_id}' updated to quantity {quantity}."
        elif action == "list":
            return inventory.list_items()
        else:
            return "Invalid action."

# Example usage
if __name__ == "__main__":
    # Create Inventory
    inventory = Inventory()

    # Load inventory from file if exists
    inventory.load_from_file("inventory.json")

    # Create a User
    user = User("admin_user", "admin")
    print(user.login())
    print(user.get_permissions())

    # Create some Items
    item1 = Item(item_id=1, name="Soccer Ball", category="Equipment", size="Size 5", color="White", quantity=10)
    item2 = Item(item_id=2, name="Tennis Racket", category="Equipment", size="Standard", color="Red", quantity=3)

    # User adds items to the inventory
    print(user.perform_inventory_actions(inventory, "add", item1))
    print(user.perform_inventory_actions(inventory, "add", item2))

    # List current items in inventory
    print("Current Inventory:")
    print(inventory.list_items())

    # Generate reports
    report = Report(inventory)
    print("Low Stock Report:")
    print(report.low_stock_report())
    
    # Save inventory to file
    inventory.save_to_file("inventory.json")
