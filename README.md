class Item:
    def __init__(self, item_id, name, category, size=None, color=None, expiration_date=None, quantity=0):
        self.item_id = item_id 
        self.name = name  
        self.category = category  
        self.size = size  
        self.color = color  
        self.expiration_date = expiration_date  
        self.quantity = quantity  

    def update_quantity(self, amount):
        self.quantity += amount

    def get_details(self):
        return (f"ID: {self.item_id}, Name: {self.name}, Category: {self.category}, "
                f"Size: {self.size}, Color: {self.color}, Expiry: {self.expiration_date}, "
                f"Quantity: {self.quantity}")
class Inventory:
    def __init__(self):
        self.items = []  

    def add_item(self, item):
        self.items.append(item)

    def remove_item(self, item_id):
        self.items = [item for item in self.items if item.item_id != item_id]

    def get_item(self, item_id):
        for item in self.items:
            if item.item_id == item_id:
                return item
        return None

    def list_items(self):
        return [item.get_details() for item in self.items]

    def update_inventory(self, item_id, amount):
        item = self.get_item(item_id)
        if item:
            item.update_quantity(amount)

    def check_stock(self, item_id):
        item = self.get_item(item_id)
        return item.quantity if item else 0
class Report:
    def __init__(self, inventory):
        self.inventory = inventory

    def low_stock_report(self, threshold=5):
        return [item.get_details() for item in self.inventory.items if item.quantity < threshold]

    def expiry_report(self):
        from datetime import datetime
        current_date = datetime.now()
        return [item.get_details() for item in self.inventory.items if item.expiration_date and item.expiration_date < current_date]

    def sales_report(self):
        return "Sales report generation not yet implemented."

    def generate_report(self):
        return {
            "low_stock": self.low_stock_report(),
            "expiry_items": self.expiry_report()
        }
class User:
    def __init__(self, username, role):
        self.username = username  
        self.role = role  # Role of the user (admin, manager)

    def login(self):
        return f"{self.username} logged in as {self.role}."

    def get_permissions(self):
        permissions = {
            "admin": ["add_item", "remove_item", "update_inventory", "generate_reports"],
            "manager": ["add_item", "update_inventory"]
        }
        return permissions.get(self.role, [])

    def perform_inventory_actions(self, action, *args):
        pass
import json

class InventoryManager:
    def __init__(self, inventory):
        self.inventory = inventory

    def save_to_file(self, filename):
        with open(filename, 'w') as f:
            json.dump([item.__dict__ for item in self.inventory.items], f)

    def load_from_file(self, filename):
        with open(filename, 'r') as f:
            items_data = json.load(f)
            self.inventory.items = [Item(**item) for item in items_data]

inventory = Inventory()
report = Report(inventory)

user = User(username="admin", role="admin")
print(user.login())

item1 = Item(item_id=1, name="Soccer Ball", category="Balls", size="Medium", color="White", quantity=20);
item2 = Item(item_id=2, name="Tennis Rackets", category="Rackets", size="Mid-Plus", color="Blue", quantity=25);
item3 = Item(item_id=3, name="Skate Boards", category="Boards", size="Full", color="Black", quantity=10);
inventory.add_item(item1);
inventory.add_item(item2);
inventory.add_item(item3);

print(inventory.list_items())

print(report.low_stock_report())
