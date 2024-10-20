class Item:
    def __init__(self, item_id, name, category, attributes, quantity=0):
        """
        Initializes an Item object.
        
        :param item_id: Unique identifier for the item
        :param name: Name of the item
        :param category: Category of the item (e.g., 'balls', 'bats', etc.)
        :param attributes: Dictionary containing specific attributes (size, color, etc.)
        :param quantity: Available quantity of the item
        """
        self.item_id = item_id
        self.name = name
        self.category = category
        self.attributes = attributes
        self.quantity = quantity

    def update_quantity(self, amount):
        """
        Updates the quantity of the item.
        
        :param amount: Amount to add/subtract from the current quantity
        """
        self.quantity += amount

    def get_details(self):
        """
        Returns a string with details of the item.
        
        :return: String representation of the item details
        """
        return {
            "ID": self.item_id,
            "Name": self.name,
            "Category": self.category,
            "Attributes": self.attributes,
            "Quantity": self.quantity
        }
class Inventory:
    def __init__(self):
        """ Initializes an empty inventory list. """
        self.items = []

    def add_item(self, item):
        """
        Adds an Item object to the inventory.
        
        :param item: Item object to be added
        """
        self.items.append(item)

    def remove_item(self, item_id):
        """
        Removes an Item object from the inventory based on its ID.
        
        :param item_id: ID of the item to be removed
        :return: True if the item was removed, False if not found
        """
        for item in self.items:
            if item.item_id == item_id:
                self.items.remove(item)
                return True
        return False

    def get_item(self, item_id):
        """
        Retrieves an Item object from the inventory by its ID.
        
        :param item_id: ID of the item to retrieve
        :return: Item object if found, None otherwise
        """
        for item in self.items:
            if item.item_id == item_id:
                return item
        return None

    def list_items(self):
        """
        Returns a list of all items in the inventory.
        
        :return: List of Item objects
        """
        return [item.get_details() for item in self.items]

    def update_inventory(self, item_id, amount):
        """
        Updates the quantity of a specified item in the inventory.
        
        :param item_id: ID of the item to update
        :param amount: Amount to add/subtract from the item's quantity
        :return: True if updated, False if item not found
        """
        item = self.get_item(item_id)
        if item:
            item.update_quantity(amount)
            return True
        return False

    def check_stock(self):
        """
        Checks stock levels for all items.
        
        :return: Dictionary of items with their stock levels
        """
        return {item.name: item.quantity for item in self.items}
class Report:
    def __init__(self, inventory):
        """
        Initializes a Report object with a reference to an Inventory object.
        
        :param inventory: Reference to an Inventory object
        """
        self.inventory = inventory

    def low_stock_report(self, threshold=5):
        """
        Generates a report of items below the specified stock threshold.
        
        :param threshold: Stock level to trigger a low stock report
        :return: List of items below the threshold
        """
        return [item.get_details() for item in self.inventory.items if item.quantity < threshold]

    def expiry_report(self, current_date):
        """
        Generates a report of items that are expired based on the current date.
        
        :param current_date: Date to compare against expiration dates
        :return: List of expired items
        """
        return [item.get_details() for item in self.inventory.items if "expiry_date" in item.attributes and item.attributes["expiry_date"] < current_date]

    def generate_report(self):
        """
        Generates a general report of the inventory.
        
        :return: Dictionary summarizing inventory status
        """
        report = {
            "Total Items": len(self.inventory.items),
            "Low Stock Items": self.low_stock_report(),
            "Stock Levels": self.inventory.check_stock()
        }
        return report
class User:
    def __init__(self, username, role):
        """
        Initializes a User object.
        
        :param username: Username of the user
        :param role: Role of the user (e.g., 'admin', 'manager')
        """
        self.username = username
        self.role = role

    def login(self):
        """
        Simulates user login.
        
        :return: Confirmation message of successful login
        """
        return f"{self.username} logged in as {self.role}."

    def get_permissions(self):
        """
        Returns the permissions associated with the user's role.
        
        :return: List of permissions
        """
        permissions = {
            'admin': ['add_item', 'remove_item', 'update_inventory', 'view_reports'],
            'manager': ['view_reports', 'update_inventory']
        }
        return permissions.get(self.role, [])

    def perform_inventory_actions(self, action, *args):
        """
        Performs an action based on user permissions.
        
        :param action: Action to perform
        :param args: Additional arguments for the action
        :return: Result of the action or error message
        """
        if action in self.get_permissions():
            if action == 'add_item':
                inventory.add_item(*args)
                return "Item added."
            elif action == 'remove_item':
                inventory.remove_item(*args[0])
                return "Item removed."
            elif action == 'update_inventory':
                inventory.update_inventory(*args)
                return "Inventory updated."
            elif action == 'view_reports':
                return "Reports viewed."
        else:
            return "Permission denied."
import json

class InventoryManager:
    @staticmethod
    def save_to_file(inventory, filename='inventory.json'):
        """
        Saves inventory data to a JSON file.
        
        :param inventory: Inventory object to save
        :param filename: Name of the file to save to
        """
        with open(filename, 'w') as file:
            json.dump([item.get_details() for item in inventory.items], file)

    @staticmethod
    def load_from_file(filename='inventory.json'):
        """
        Loads inventory data from a JSON file.
        
        :param filename: Name of the file to load from
        :return: List of Item objects
        """
        with open(filename, 'r') as file:
            items_data = json.load(file)
            return [Item(**item) for item in items_data]
