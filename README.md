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
