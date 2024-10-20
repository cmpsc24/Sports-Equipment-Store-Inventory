class Item:
    def __init__(self, id: int, name: str, category: str, attributes: dict, quantity: int):
        """Initializes an item with the given attributes."""
        self.id = id
        self.name = name
        self.category = category
        self.attributes = attributes
        self.quantity = quantity

    def update_quantity(self, amount: int):
        """Updates the quantity of the item."""
        self.quantity += amount

    def get_details(self) -> dict:
        """Returns the details of the item."""
        return {
            "ID": self.id,
            "Name": self.name,
            "Category": self.category,
            "Attributes": self.attributes,
            "Quantity": self.quantity
        }
class Inventory:
    def __init__(self):
        """Initializes an empty inventory."""
        self.items = []

    def add_item(self, item: Item):
        """Adds an item to the inventory."""
        self.items.append(item)

    def remove_item(self, item_id: int):
        """Removes an item from the inventory by ID."""
        self.items = [item for item in self.items if item.id != item_id]

    def get_item(self, item_id: int) -> Item:
        """Retrieves an item by ID."""
        for item in self.items:
            if item.id == item_id:
                return item
        return None

    def list_items(self) -> list:
        """Lists all items in the inventory."""
        return [item.get_details() for item in self.items]

    def update_inventory(self, item_id: int, amount: int):
        """Updates the quantity of a specific item."""
        item = self.get_item(item_id)
        if item:
            item.update_quantity(amount)

    def check_stock(self, item_id: int) -> int:
        """Checks the stock level of an item."""
        item = self.get_item(item_id)
        return item.quantity if item else 0
class Report:
    def __init__(self, inventory: Inventory):
        """Initializes the report with the given inventory."""
        self.inventory = inventory

    def low_stock_report(self, threshold: int) -> list:
        """Generates a report of items below a specified stock threshold."""
        return [item.get_details() for item in self.inventory.items if item.quantity < threshold]

    def expiry_report(self) -> list:
        """Generates a report of items nearing their expiration date."""
        return [item.get_details() for item in self.inventory.items if 'expiration' in item.attributes]

    def sales_report(self) -> dict:
        """Generates a sales report based on the current inventory."""
        return {item.name: item.quantity for item in self.inventory.items}

    def generate_report(self, report_type: str, *args) -> list:
        """Generates different types of reports based on the report type."""
        if report_type == 'low_stock':
            return self.low_stock_report(*args)
        elif report_type == 'expiry':
            return self.expiry_report()
        # Additional report types can be added here.
class User:
    def __init__(self, username: str, role: str):
        """Initializes a user with a username and role."""
        self.username = username
        self.role = role

    def login(self) -> bool:
        """Authenticates the user (placeholder for actual logic)."""
        return True  # Placeholder for actual login logic

    def get_permissions(self) -> list:
        """Returns a list of permissions based on the user’s role."""
        permissions = {
            'admin': ['add', 'remove', 'update', 'view', 'generate_reports'],
            'manager': ['view', 'generate_reports'],
        }
        return permissions.get(self.role, [])

    def perform_inventory_actions(self, action: str, item: Item, inventory: Inventory):
        """Performs inventory-related actions based on user permissions."""
        if action not in self.get_permissions():
            raise PermissionError("You do not have permission to perform this action.")
        if action == 'add':
            inventory.add_item(item)
        elif action == 'remove':
            inventory.remove_item(item.id)
        elif action == 'update':
            inventory.update_inventory(item.id, item.quantity)
import json

class InventoryManager:
    def __init__(self, inventory: Inventory, filename: str):
        """Initializes the inventory manager with an inventory and a filename for data persistence."""
        self.inventory = inventory
        self.filename = filename

    def save_to_file(self):
        """Saves the inventory data to a JSON file."""
        with open(self.filename, 'w') as file:
            json.dump([item.get_details() for item in self.inventory.items], file)

    def load_from_file(self):
        """Loads the inventory data from a JSON file."""
        try:
            with open(self.filename, 'r') as file:
                items_data = json.load(file)
                for item_data in items_data:
                    item = Item(
                        id=item_data['ID'],
                        name=item_data['Name'],
                        category=item_data['Category'],
                        attributes=item_data['Attributes'],
                        quantity=item_data['Quantity']
                    )
                    self.inventory.add_item(item)
        except FileNotFoundError:
            print("No previous inventory file found.")
# Create inventory
inventory = Inventory()

# Create items
item1 = Item(id=1, name="Soccer Ball", category="Ball", attributes={"size": "5", "color": "white"}, quantity=10)
item2 = Item(id=2, name="Tennis Racket", category="Racket", attributes={"size": "medium", "color": "yellow"}, quantity=5)

# Add items to inventory
inventory.add_item(item1)
inventory.add_item(item2)

# Create a report
report = Report(inventory)

# Generate low stock report
low_stock = report.low_stock_report(threshold=6)
print("Low Stock Report:", low_stock)  # Should show items below stock level of 6

# User management
admin = User(username="admin", role="admin")
if admin.login():
    admin.perform_inventory_actions('remove', item1, inventory)  # Remove item1 if permission granted

# Save inventory to file
manager = InventoryManager(inventory,
