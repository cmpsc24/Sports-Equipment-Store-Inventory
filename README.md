import json
from datetime import datetime


# Class: Item
class Item:
    """Represents an individual item in the inventory."""

    def __init__(self, item_id, name, category, size=None, color=None, expiration_date=None):
        self.item_id = item_id
        self.name = name
        self.category = category
        self.size = size
        self.color = color
        self.expiration_date = expiration_date
        self.quantity = 0

    def update_quantity(self, quantity):
        """Update the quantity of the item."""
        self.quantity += quantity

    def get_details(self):
        """Return the details of the item as a dictionary."""
        details = {
            'ID': self.item_id,
            'Name': self.name,
            'Category': self.category,
            'Size': self.size,
            'Color': self.color,
            'Expiration Date': self.expiration_date,
            'Quantity': self.quantity
        }
        return details

    def to_dict(self):
        """Convert item to a dictionary for JSON serialization."""
        return {
            'item_id': self.item_id,
            'name': self.name,
            'category': self.category,
            'size': self.size,
            'color': self.color,
            'expiration_date': self.expiration_date.isoformat() if self.expiration_date else None,
            'quantity': self.quantity
        }


# Class: Inventory
class Inventory:
    """Manages a collection of Item objects."""

    def __init__(self):
        self.items = []

    def add_item(self, item):
        """Add a new item to the inventory."""
        self.items.append(item)

    def remove_item(self, item_id):
        """Remove an item from the inventory by ID."""
        self.items = [item for item in self.items if item.item_id != item_id]

    def get_item(self, item_id):
        """Get an item by its ID."""
        for item in self.items:
            if item.item_id == item_id:
                return item
        return None

    def list_items(self):
        """Return a list of all items in the inventory."""
        return [item.get_details() for item in self.items]

    def update_inventory(self, item_id, quantity):
        """Update the quantity of a specific item."""
        item = self.get_item(item_id)
        if item:
            item.update_quantity(quantity)

    def check_stock(self):
        """Return a dictionary of item IDs and their respective quantities."""
        return {item.item_id: item.quantity for item in self.items}

    def save_to_file(self, filename):
        """Save inventory data to a JSON file."""
        with open(filename, 'w') as f:
            json.dump([item.to_dict() for item in self.items], f)

    def load_from_file(self, filename):
        """Load inventory data from a JSON file."""
        try:
            with open(filename, 'r') as f:
                items_data = json.load(f)
                self.items = [
                    Item(
                        item_id=item['item_id'],
                        name=item['name'],
                        category=item['category'],
                        size=item['size'],
                        color=item['color'],
                        expiration_date=datetime.fromisoformat(item['expiration_date']) if item['expiration_date'] else None,
                    )
                    for item in items_data
                ]
        except FileNotFoundError:
            print("File not found. Starting with an empty inventory.")


# Class: Report
class Report:
    """Generates reports based on the current state of the inventory."""

    def __init__(self, inventory):
        self.inventory = inventory

    def low_stock_report(self, threshold=5):
        """Generate a report of items with stock below the specified threshold."""
        low_stock_items = [item.get_details() for item in self.inventory.items if item.quantity < threshold]
        return low_stock_items

    def expiry_report(self):
        """Generate a report of expired items."""
        today = datetime.now()
        expiring_items = [
            item.get_details() for item in self.inventory.items 
            if item.expiration_date and item.expiration_date < today
        ]
        return expiring_items

    def sales_report(self):
        """Generate a report of current stock."""
        return self.inventory.check_stock()

    def generate_report(self, report_type):
        """Generate a specific type of report."""
        if report_type == 'low_stock':
            return self.low_stock_report()
        elif report_type == 'expiry':
            return self.expiry_report()
        elif report_type == 'sales':
            return self.sales_report()
        else:
            return "Invalid report type"


# Class: User
class User:
    """Handles user authentication and permissions."""

    def __init__(self, username, role):
        self.username = username
        self.role = role

    def login(self):
        """Simulate user login."""
        print(f"{self.username} logged in as {self.role}")

    def get_permissions(self):
        """Get the permissions based on the user's role."""
        permissions = {
            'admin': ['add_item', 'remove_item', 'update_inventory', 'generate_report'],
            'manager': ['update_inventory', 'generate_report'],
            'staff': ['update_inventory'],  # Additional role example
        }
        return permissions.get(self.role, [])

    def perform_inventory_action(self, action, *args):
        """Perform an action on the inventory if permitted."""
        if action in self.get_permissions():
            return action(*args)
        else:
            return "Permission denied"


# Example usage
if __name__ == "__main__":
    # Create an inventory instance
    inventory = Inventory()

    # Load inventory from a file (if exists)
    inventory.load_from_file('inventory.json')

    # Create some item instances
    ball = Item(item_id=1, name='Soccer Ball', category='Balls', size='Size 5', color='White')
    tennis_racket = Item(item_id=2, name='Tennis Racket', category='Rackets', size='Standard', color='Red', expiration_date=datetime(2025, 1, 1))

    # Add items to the inventory
    inventory.add_item(ball)
    inventory.add_item(tennis_racket)

    # Update item quantities
    inventory.update_inventory(item_id=1, quantity=10)
    inventory.update_inventory(item_id=2, quantity=3)

    # Generate and print reports
    report = Report(inventory)
    print("Low Stock Report:", report.low_stock_report())
    print("Expiry Report:", report.expiry_report())
    print("Sales Report:", report.sales_report())

    # User management
    admin_user = User(username='admin_user', role='admin')
    admin_user.login()
    print("Admin Permissions:", admin_user.get_permissions())

    # Perform actions
    if 'add_item' in admin_user.get_permissions():
        inventory.add_item(Item(item_id=3, name='Basketball', category='Balls', size='Size 7', color='Orange'))
        print("Added Basketball to inventory.")

    # Save inventory to file
    inventory.save_to_file('inventory.json')

    # List items in inventory
    print("Inventory Items:", inventory.list_items())
