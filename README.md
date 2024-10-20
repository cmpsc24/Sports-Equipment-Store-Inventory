class Item:
    def __init__(self, id: int, name: str, category: str, specific_attributes: dict, quantity: int):
        self.id = id
        self.name = name
        self.category = category
        self.specific_attributes = specific_attributes
        self.quantity = quantity

    def update_quantity(self, amount: int):
        self.quantity += amount

    def get_details(self):
        return f"ID: {self.id}, Name: {self.name}, Category: {self.category}, " \
               f"Attributes: {self.specific_attributes}, Quantity: {self.quantity}"
class Inventory:
    def __init__(self):
        self.items = []

    def add_item(self, item: Item):
        self.items.append(item)

    def remove_item(self, item_id: int):
        self.items = [item for item in self.items if item.id != item_id]

    def get_item(self, item_id: int):
        for item in self.items:
            if item.id == item_id:
                return item
        return None

    def list_items(self):
        return [item.get_details() for item in self.items]

    def update_inventory(self, item_id: int, amount: int):
        item = self.get_item(item_id)
        if item:
            item.update_quantity(amount)

    def check_stock(self):
        return [(item.name, item.quantity) for item in self.items]
class Report:
    def __init__(self, inventory: Inventory):
        self.inventory = inventory

    def low_stock_report(self, threshold: int):
        return [item.get_details() for item in self.inventory.items if item.quantity < threshold]

    def expiry_report(self):
        # Assuming expiration date is an attribute in specific_attributes
        return [item.get_details() for item in self.inventory.items if 'expiration_date' in item.specific_attributes]

    def sales_report(self):
        # Example: Count items by category (dummy implementation)
        report = {}
        for item in self.inventory.items:
            if item.category in report:
                report[item.category] += 1
            else:
                report[item.category] = 1
        return report

    def generate_report(self):
        low_stock = self.low_stock_report(threshold=5)
        expiry_items = self.expiry_report()
        sales = self.sales_report()
        return {
            "low_stock": low_stock,
            "expiry": expiry_items,
            "sales": sales
        }
class User:
    def __init__(self, username: str, role: str):
        self.username = username
        self.role = role

    def login(self):
        # Placeholder for actual login implementation
        return f"{self.username} logged in."

    def get_permissions(self):
        permissions = {
            "admin": ["add_item", "remove_item", "update_inventory", "generate_reports"],
            "manager": ["add_item", "update_inventory", "view_inventory"],
        }
        return permissions.get(self.role, [])

    def perform_inventory_actions(self, action: str, *args):
        permissions = self.get_permissions()
        if action in permissions:
            return f"Performing action: {action} with arguments {args}"
        else:
            return "Permission denied for this action."
# Create an inventory instance
inventory = Inventory()

# Create some items
item1 = Item(1, "Soccer Ball", "balls", {"size": "5", "color": "white"}, 10)
item2 = Item(2, "Tennis Racket", "rackets", {"size": "standard", "color": "blue"}, 4)
item3 = Item(3, "Basketball", "balls", {"size": "7", "color": "orange"}, 6)

# Add items to inventory
inventory.add_item(item1)
inventory.add_item(item2)
inventory.add_item(item3)

# List all items
print("Current Inventory:")
print(inventory.list_items())

# Update quantity of an item
inventory.update_inventory(2, 5)  # Add 5 Tennis Rackets
print("Updated Inventory:")
print(inventory.list_items())

# Generate a report
report = Report(inventory)
print("Low Stock Report:")
print(report.low_stock_report(5))
import json

class Inventory:
    # Existing methods...

    def save_to_file(self, filename: str):
        with open(filename, 'w') as f:
            json.dump([item.__dict__ for item in self.items], f)

    def load_from_file(self, filename: str):
        with open(filename, 'r') as f:
            items_data = json.load(f)
            self.items = [Item(**item_data) for item_data in items_data]

# Example of saving and loading inventory
inventory.save_to_file('inventory.json')
inventory.load_from_file('inventory.json')
