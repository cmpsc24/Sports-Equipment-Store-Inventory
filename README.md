class Item:
    def __init__(self, id, name, category, attributes, quantity):
        self.id = id
        self.name = name
        self.category = category
        self.attributes = attributes
        self.quantity = quantity

    def update_quantity(self, amount):
        self.quantity += amount

    def get_details(self):
        return {
            "ID": self.id,
            "Name": self.name,
            "Category": self.category,
            "Attributes": self.attributes,
            "Quantity": self.quantity
        }
class Inventory:
    def __init__(self):
        self.items = []

    def add_item(self, item: Item):
        self.items.append(item)

    def remove_item(self, item_id: int):
        self.items = [item for item in self.items if item.id != item_id]

    def get_item(self, item_id: int) -> Item:
        for item in self.items:
            if item.id == item_id:
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
        # Placeholder for items with expiration dates
        # Assuming we have an 'expiration' key in attributes
        return [item.get_details() for item in self.inventory.items if 'expiration' in item.attributes]

    def sales_report(self) -> dict:
        # Placeholder for a sales report; could be expanded based on sales data
        return {item.name: item.quantity for item in self.inventory.items}

    def generate_report(self, report_type: str, *args) -> list:
        if report_type == 'low_stock':
            return self.low_stock_report(*args)
        elif report_type == 'expiry':
            return self.expiry_report()
        # Add more report types as needed
class User:
    def __init__(self, username, role):
        self.username = username
        self.role = role

    def login(self):
        # Placeholder for actual login logic
        return True

    def get_permissions(self) -> list:
        permissions = {
            'admin': ['add', 'remove', 'update', 'view', 'generate_reports'],
            'manager': ['view', 'generate_reports'],
        }
        return permissions.get(self.role, [])

    def perform_inventory_actions(self, action: str, item: Item, inventory: Inventory):
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
        self.inventory = inventory
        self.filename = filename

    def save_to_file(self):
        with open(self.filename, 'w') as file:
            json.dump([item.get_details() for item in self.inventory.items], file)

    def load_from_file(self):
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

# Create report
report = Report(inventory)

# Generate low stock report
low_stock = report.low_stock_report(threshold=6)
print(low_stock)  # Should show items below stock level of 6

# User management
admin = User(username="admin", role="admin")
if admin.login():
    admin.perform_inventory_actions('remove', item1, inventory)  # Remove item1 if permission granted

# Save inventory to file
manager = InventoryManager(inventory, 'inventory.json')
manager.save_to_file()

# Load inventory from file
manager.load_from_file()
