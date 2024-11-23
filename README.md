import pandas as pd
import matplotlib.pyplot as plt

# Load the expenses from the CSV file
def load_expenses(file_path='/Users/anishaboken/Downloads/expenses.csv'):
    try:
        data = pd.read_csv(file_path)
        print("Expenses loaded successfully!")
        return data
    except FileNotFoundError:
        print("File not found. Creating a new expenses file.")
        empty_data = pd.DataFrame(columns=["Category", "Description", "Amount", "Date"])
        empty_data.to_csv(file_path, index=False)
        return empty_data

# Add a new expense entry to the CSV file
def add_expense_to_file(file_path='expenses.csv'):
    category = input("Enter the category of the expense (e.g., Food, Transport, Entertainment): ").strip()
    description = input("Enter a brief description of the expense: ").strip()
    
    try:
        amount = float(input("Enter the expense amount: "))
    except ValueError:
        print("Invalid input. Please enter a valid numeric amount.")
        return
    
    date = input("Enter the date of the expense (YYYY-MM-DD): ").strip()

    # Create a new DataFrame with the new expense
    new_expense = pd.DataFrame({
        "Category": [category],
        "Description": [description],
        "Amount": [amount],
        "Date": [date]
    })

    # Append to CSV file
    try:
        new_expense.to_csv(file_path, mode='a', header=False, index=False)
        print(f"Expense '{description}' added successfully!")
    except Exception as e:
        print(f"Error saving expense: {e}")

# Analyze and print expense summary
def generate_report(expenses_df, income):
    print("\nExpense Summary Report:")
    
    # Calculate total spent by category
    category_totals = expenses_df.groupby("Category")["Amount"].sum()
    total_spent = category_totals.sum()
    
    print(f"Total spent: ${total_spent:.2f}")
    for category, total in category_totals.items():
        percentage = (total / income) * 100
        print(f"  {category}: ${total:.2f} ({percentage:.2f}% of income)")
    
    remaining_balance = income - total_spent
    print(f"Remaining balance: ${remaining_balance:.2f}")

    # Call visualization functions
    plot_expenses_by_category(category_totals)
    plot_financial_summary(income, total_spent, remaining_balance)

def plot_expenses_by_category(category_totals):
    """Plots a pie chart of expenses by category."""
    categories = category_totals.index
    amounts = category_totals.values

    plt.figure(figsize=(8, 8))
    plt.pie(amounts, labels=categories, autopct='%1.1f%%', startangle=140)
    plt.title("Expenses by Category")
    plt.show()

def plot_financial_summary(income, total_spent, remaining_balance):
    """Plots a bar chart of total income, total spent, and remaining balance."""
    labels = ["Income", "Total Spent", "Remaining Balance"]
    values = [income, total_spent, remaining_balance]

    plt.figure(figsize=(10, 6))
    plt.bar(labels, values, color=['#4CAF50', '#FF5733', '#3498DB'])
    plt.title("Financial Summary")
    plt.ylabel("Amount ($)")
    plt.show()

def main():
    # User inputs monthly income
    try:
        income = float(input("Enter your income for this month: "))
    except ValueError:
        print("Invalid income amount. Please restart and enter a valid number.")
        return

    # Load expenses from the dataset
    expenses_df = load_expenses()

    while True:
        print("\nExpense Tracker Menu:")
        print("1. Add Expense to CSV")
        print("2. Generate Expense Report")
        print("3. Exit")
        choice = input("Enter your choice (1-3): ")

        if choice == "1":
            add_expense_to_file()
            # Reload expenses after adding a new one
            expenses_df = load_expenses()
        elif choice == "2":
            generate_report(expenses_df, income)
        elif choice == "3":
            print("Exiting Expense Tracker. Have a great day!")
            break
        else:
            print("Invalid choice. Please select 1-3.")

if __name__ == "__main__":
    main()
