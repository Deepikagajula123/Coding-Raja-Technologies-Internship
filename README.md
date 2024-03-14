# Coding-Raja-Technologies-Internship
import os
import json
from datetime import datetime

class Transaction:
    def __init__(self, amount, category, description, date=None):
        self.amount = amount
        self.category = category
        self.description = description
        self.date = date if date else datetime.now().strftime("%Y-%m-%d")

    def __str__(self):
        return f"{self.date} - {self.amount} ({self.category}): {self.description}"

class BudgetTracker:
    def __init__(self):
        self.transactions = []

    def add_transaction(self, transaction):
        self.transactions.append(transaction)

    def calculate_budget(self):
        income = sum(transaction.amount for transaction in self.transactions if transaction.amount > 0)
        expenses = sum(transaction.amount for transaction in self.transactions if transaction.amount < 0)
        return income + expenses

    def analyze_expenses(self):
        expense_categories = {}
        for transaction in self.transactions:
            if transaction.amount < 0:
                category = transaction.category
                if category in expense_categories:
                    expense_categories[category] += abs(transaction.amount)
                else:
                    expense_categories[category] = abs(transaction.amount)
        return expense_categories

    def list_transactions(self):
        if not self.transactions:
            print("No transactions.")
        else:
            for transaction in self.transactions:
                print(transaction)

    def save_to_file(self, filename):
        with open(filename, 'w') as f:
            json.dump([transaction.__dict__ for transaction in self.transactions], f)

    def load_from_file(self, filename):
        if os.path.exists(filename):
            with open(filename, 'r') as f:
                data = json.load(f)
                self.transactions = [Transaction(**transaction) for transaction in data]

def main():
    budget_tracker = BudgetTracker()
    filename = "transactions.json"
    budget_tracker.load_from_file(filename)

    while True:
        print("\nCommand Options:")
        print("1. Add Income")
        print("2. Add Expense")
        print("3. View Transactions")
        print("4. View Remaining Budget")
        print("5. Analyze Expenses")
        print("6. Save and Exit")

        choice = input("Enter your choice: ")

        if choice == "1":
            amount = float(input("Enter income amount: "))
            category = input("Enter income category: ")
            description = input("Enter income description: ")
            transaction = Transaction(amount, category, description)
            budget_tracker.add_transaction(transaction)
        elif choice == "2":
            amount = float(input("Enter expense amount: "))
            category = input("Enter expense category: ")
            description = input("Enter expense description: ")
            transaction = Transaction(-amount, category, description)
            budget_tracker.add_transaction(transaction)
        elif choice == "3":
            budget_tracker.list_transactions()
        elif choice == "4":
            remaining_budget = budget_tracker.calculate_budget()
            print(f"Remaining Budget: {remaining_budget}")
        elif choice == "5":
            expense_categories = budget_tracker.analyze_expenses()
            print("Expense Analysis:")
            for category, amount in expense_categories.items():
                print(f"{category}: {amount}")
        elif choice == "6":
            budget_tracker.save_to_file(filename)
            print("Transactions saved. Exiting...")
            break
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    main()
