# smart-pantry

Below is a complete Python program for `smart-pantry`, a simple console-based application to manage household inventory by tracking item expiration dates and suggesting recipes. We'll use the following libraries in the program: `datetime` for managing dates and `json` for data storage. We'll define our basic functionality, including adding, removing, and checking inventory items, as well as suggesting recipes based on available ingredients.

```python
import json
from datetime import datetime, timedelta

# Sample recipe suggestions based on available items
RECIPES = {
    'Pasta': ['Pasta', 'Tomato Sauce', 'Cheese'],
    'Salad': ['Lettuce', 'Tomato', 'Cucumber'],
    'Sandwich': ['Bread', 'Lettuce', 'Cheese', 'Ham']
}

class SmartPantry:
    def __init__(self, inventory_file='inventory.json'):
        self.inventory_file = inventory_file
        self.inventory = self.load_inventory()
    
    def load_inventory(self):
        """
        Load inventory from a JSON file.
        Returns an empty inventory if the file doesn't exist.
        """
        try:
            with open(self.inventory_file, 'r') as file:
                return json.load(file)
        except FileNotFoundError:
            return {}
        except json.JSONDecodeError:
            print("Error decoding inventory file. Starting with an empty inventory.")
            return {}
    
    def save_inventory(self):
        """
        Save the current inventory to a JSON file.
        """
        try:
            with open(self.inventory_file, 'w') as file:
                json.dump(self.inventory, file)
        except IOError:
            print("Error saving inventory to file.")

    def add_item(self, item_name, quantity, expiration_date):
        """
        Add an item to the inventory.
        """
        try:
            expiration_date_dt = datetime.strptime(expiration_date, '%Y-%m-%d')
            if item_name in self.inventory:
                self.inventory[item_name]['quantity'] += quantity
            else:
                self.inventory[item_name] = {'quantity': quantity, 'expiration_date': expiration_date}

            self.save_inventory()
            print(f"Added {quantity} of {item_name} with expiration date {expiration_date}.")
        except ValueError:
            print("Error: Invalid expiration date format. Please use YYYY-MM-DD.")
    
    def remove_item(self, item_name, quantity):
        """
        Remove an item or reduce its quantity in the inventory.
        """
        if item_name in self.inventory:
            if self.inventory[item_name]['quantity'] > quantity:
                self.inventory[item_name]['quantity'] -= quantity
                print(f"Removed {quantity} of {item_name}.")
            elif self.inventory[item_name]['quantity'] == quantity:
                del self.inventory[item_name]
                print(f"Removed all of {item_name}.")
            else:
                print("Error: Not enough quantity to remove.")
        else:
            print("Error: Item not found in inventory.")
        
        self.save_inventory()

    def check_expiration_dates(self):
        """
        Check for expired items in the inventory.
        """
        today = datetime.now().date()
        expired_items = [item for item, details in self.inventory.items() if datetime.strptime(details['expiration_date'], '%Y-%m-%d').date() < today]
        
        if expired_items:
            print("Warning: The following items are expired:")
            for item in expired_items:
                print(f"- {item} (expired on {self.inventory[item]['expiration_date']})")
        else:
            print("No expired items found.")

    def suggest_recipes(self):
        """
        Suggest recipes based on available items in the inventory.
        """
        available_items = set(self.inventory.keys())
        suggestions = []

        for recipe, ingredients in RECIPES.items():
            if all(item in available_items for item in ingredients):
                suggestions.append(recipe)
        
        if suggestions:
            print("Recipes you can make:")
            for recipe in suggestions:
                print(f"- {recipe}")
        else:
            print("No recipes can be made with the available ingredients.")

def main():
    pantry = SmartPantry()
    
    while True:
        print("\nSmart Pantry Menu:")
        print("1. Add Item")
        print("2. Remove Item")
        print("3. Check Expiration Dates")
        print("4. Suggest Recipes")
        print("5. Quit")
        
        choice = input("Choose an option (1-5): ")
        
        if choice == '1':
            item_name = input("Enter item name: ")
            quantity = int(input("Enter quantity: "))
            expiration_date = input("Enter expiration date (YYYY-MM-DD): ")
            pantry.add_item(item_name, quantity, expiration_date)
        elif choice == '2':
            item_name = input("Enter item name: ")
            quantity = int(input("Enter quantity to remove: "))
            pantry.remove_item(item_name, quantity)
        elif choice == '3':
            pantry.check_expiration_dates()
        elif choice == '4':
            pantry.suggest_recipes()
        elif choice == '5':
            print("Exiting Smart Pantry. Goodbye!")
            break
        else:
            print("Invalid option. Please choose again.")

if __name__ == "__main__":
    main()
```

### Key Components:
- **Inventory Management**: Allows users to add and remove items from their pantry.
- **Expiration Tracking**: Provides functionality to check for expired items.
- **Recipe Suggestions**: Uses available ingredients to suggest possible recipes.
- **Error Handling**: Ensures correct date formats and handles file input/output errors.
- **Data Persistence**: Saves inventory data using JSON files, making it persistent across sessions.

Optional enhancements might include a GUI version using libraries such as `tkinter` or `PyQt`, or expanding the recipe database.