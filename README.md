# budget tracker 

import csv
from datetime import datetime

FILE = "transactions.csv"



def load_data():
    try:
        with open(FILE, "r", newline="") as f:
            return list(csv.DictReader(f))
    except FileNotFoundError:
        return []



def save_data(t):
    with open(FILE, "w", newline="") as f:
        fields = ["id", "type", "amount", "category", "date", "note"]

        w = csv.DictWriter(f, fieldnames=fields)
        w.writeheader()
        w.writerows(t)



def add_transaction(t):
    while True:
        typ = input("Type (income/expense): ").strip().lower()

        if typ in ["income", "expense"]:
            break

        print("Please enter income or expense.")


    while True:
        try:
            amt = float(input("Amount: "))

            if amt > 0:
                break

            print("Amount must be greater than 0.")

        except ValueError:
            print("Please enter a valid number.")

  
    while True:
        cat = input("Category: ").strip()

        if cat:
            break

        print("Category cannot be blank.")

    
    while True:
        date = input("Date (YYYY-MM-DD) [today]: ").strip()

        if date == "":
            date = datetime.today().strftime("%Y-%m-%d")
            break

        try:
            datetime.strptime(date, "%Y-%m-%d")
            break
        except ValueError:
            print("Invalid date. Use YYYY-MM-DD.")

    note = input("Note: ").strip()

    x = {
        "id": str(len(t) + 1),
        "type": typ,
        "amount": str(amt),
        "category": cat,
        "date": date,
        "note": note
    }

    t.append(x)
    save_data(t)

    print("Transaction added and saved.")



def summary(t):
    inc = 0
    exp = 0

    for x in t:
        if x["type"] == "income":
            inc += float(x["amount"])
        else:
            exp += float(x["amount"])

    print("\n--- Summary ---")
    print(f"Total Income:   ₹{inc:.2f}")
    print(f"Total Expenses: ₹{exp:.2f}")
    print(f"Net Balance:    ₹{inc - exp:.2f}")



def category(t):
    if not t:
        print("\nNo transactions found.")
        return

    d = {}

    for x in t:
        c = x["category"]

        if c not in d:
            d[c] = [0, 0]

        if x["type"] == "income":
            d[c][0] += float(x["amount"])
        else:
            d[c][1] += float(x["amount"])

    print("\n--- By Category ---")

    for c in d:
        print(f"\n{c}")
        print(f"  Income:  ₹{d[c][0]:.2f}")
        print(f"  Expense: ₹{d[c][1]:.2f}")



def show(t):
    if not t:
        print("\nNo transactions found.")
        return

    print("\n--- Transactions ---")

    for x in t:
        print(
            f"ID: {x['id']} | "
            f"{x['date']} | "
            f"{x['type']} | "
            f"₹{float(x['amount']):.2f} | "
            f"{x['category']} | "
            f"{x['note']}"
        )



def delete_transaction(t):
    show(t)

    if not t:
        return

    try:
        i = input("\nEnter transaction ID to delete: ")

        for x in t:
            if x["id"] == i:
                t.remove(x)
                save_data(t)
                print("Transaction deleted.")
                return

        print("Transaction ID not found.")

    except ValueError:
        print("Invalid ID.")



def edit_transaction(t):
    show(t)

    if not t:
        return

    i = input("\nEnter transaction ID to edit: ")

    for x in t:
        if x["id"] == i:

            print("Leave blank to keep the old value.")

            typ = input(
                f"Type ({x['type']}): "
            ).strip().lower()

            if typ in ["income", "expense"]:
                x["type"] = typ

            amt = input(
                f"Amount ({x['amount']}): "
            ).strip()

            if amt:
                try:
                    if float(amt) > 0:
                        x["amount"] = amt
                    else:
                        print("Amount must be greater than 0.")
                except ValueError:
                    print("Invalid amount.")

            cat = input(
                f"Category ({x['category']}): "
            ).strip()

            if cat:
                x["category"] = cat

            date = input(
                f"Date ({x['date']}): "
            ).strip()

            if date:
                try:
                    datetime.strptime(date, "%Y-%m-%d")
                    x["date"] = date
                except ValueError:
                    print("Invalid date.")

            note = input(
                f"Note ({x['note']}): "
            ).strip()

            if note:
                x["note"] = note

            save_data(t)
            print("Transaction updated and saved.")
            return

    print("Transaction ID not found.")



def main():
    t = load_data()

    print("\nWelcome to Budget Tracker!")

    while True:
        print("""
1. Add transaction
2. View summary
3. View by category
4. Delete transaction
5. Edit transaction
6. Exit
""")

        ch = input("> ").strip()

        if ch == "1":
            add_transaction(t)

        elif ch == "2":
            summary(t)

        elif ch == "3":
            category(t)

        elif ch == "4":
            delete_transaction(t)

        elif ch == "5":
            edit_transaction(t)

        elif ch == "6":
            print("Thank you for using Budget Tracker!")
            break

        else:
            print("Invalid choice. Please select 1-6.")


main()
