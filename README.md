Decription of the Project : 
The Sports Equipment Store Inventory System manages sports equipment inventory efficiently. It enables users to add, remove, update, and view sports equipment items. The system generates reports on stock levels and expiration dates of items, and has user role management, allowing different permissions for administrators and managers. This code defines specific attributes and methods for each class which ensures clear separation of class system such as it has Item class – which manages individual item properties like ID, name, category, etc., Inventory Class – which handles collections of Item objects, including operations like adding and removing items, Report Class – which focuses on generating inventory-related reports, and User Class – which manages user roles and permissions. For the output, when the item item1 is added to the inventory, there’s no direct output for this operation, but the item is now part of the inventory as inventory.list_items() method gets details of all items in the inventory. Since we added more items, the expected output will be a list containing that item's details, then the report.low_stock_report() method checks for items with quantities and since there are no items in the low stock report. The expected output will be an empty list [].  

 
Flowhchart description : 
Item Class: Represents individual items in the inventory, with attributes such as ID, name, category, size, color, expiration date, and quantity. It includes methods for updating quantities and retrieving item details. 
Inventory Class: Manages a collection of Item objects. Which includes methods to add, remove, retrieve, list items, and check stock levels. 
Report Class: Generates various reports based on inventory data. Which includes methods for low stock reports and expiration reports. 
User Class: Handles user information and permissions. Which includes methods for logging in and checking permissions. 


Instructions on how to use it for full functionalities provided by your code : 
First to Initialize the System: create an instance of the Inventory class and then create an instance of the Report class, passing the inventory instance. Then, User Login: create a User instance with a username and role and call the login() method to log in. Then, Add Items: create Item instances with required attributes. Use the add_item() method of the Inventory class to add items. Then, List Items: call list_items() on the Inventory instance to view all items, and finally Generate Reports: call low_stock_report() or other report methods to generate reports based on the current inventory. 


Verification of the sanity of the code to check the validity of the implemented functionalities : 
To verify the functionality of the code - Create test cases for each method in the Item, Inventory, Report, and User classes, ensure that methods handle invalid inputs and test the interaction between classes to know if they work together as expected. 
Adding Items - 
item1 = Item(item_id=1, name="Soccer Ball", category="Balls", size="Medium", color="White", quantity=20);
inventory.add_item(item1) 
Generating Reports - 
print(report.low_stock_report())  
Create inventory and report instances
inventory = Inventory() 
report = Report(inventory) 
User and log in
user = User(username="admin", role="admin") 
print(user.login()) 


Discuss your finding through this project, challenges faced during implementation, any limitations or areas for improvement :
Findings - The approach using classes allows for easier access of the code and helps in managing permissions effectively. 
Challenges Faced - Data consistency during updates was challenging and implementing a user-friendly interface for interactions. 
Areas for Improvement - adding a user interface to make the system more accessible and implementing reporting features such as sales trends. 

OUTPUT : 
admin logged in as admin.
['ID: 1, Name: Soccer Ball, Category: Balls, Size: Medium, Color: White, Expiry: None, Quantity: 20',
'ID: 2, Name: Tennis Rackets, Category: Rackets, Size: Mid-Plus, Color: Blue, Expiry: None, Quantity: 25', 
'ID: 3, Name: Skate Boards, Category: Boards, Size: Full, Color: Black, Expiry: None, Quantity: 10', 'ID: 4, Name: Badminton Rackets, Category: Rackets, Size: Mid-Medium, Color: Red, Expiry: None, Quantity: 25', 
'ID: 5, Name: FootBall, Category: Balls, Size: Medium, Color: Brown, Expiry: None, Quantity: 30']
[]
