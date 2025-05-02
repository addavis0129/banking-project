import sqlite3

conn = sqlite3.connect('bank.db')
cursor = conn.cursor()

cursor.execute('''
    CREATE TABLE IF NOT EXISTS accounts (
        name TEXT,
        balance REAL
    )
''')

name = "Anthony Davis"
balance = 1000.0

cursor.execute("SELECT * FROM accounts WHERE name = ?", (name,))
account = cursor.fetchone()

if account:
    balance = account[1]
else:
    cursor.execute("INSERT INTO accounts VALUES (?, ?)", (name, balance))
    conn.commit()

while True:
    print(f"\nWelcome, {name}!")
    print(f"Your balance is: ${balance:.2f}")
    print("1. Deposit")
    print("2. Withdraw")
    print("3. Exit")
    choice = input("What would you like to do? ")

    if choice == "1":
        amount = float(input("Enter amount to deposit: $"))
        balance += amount
        cursor.execute("UPDATE accounts SET balance = ? WHERE name = ?", (balance, name))
        conn.commit()
    elif choice == "2":
        amount = float(input("Enter amount to withdraw: $"))
        if amount > balance:
            print("Insufficient funds!")
        else:
            balance -= amount
            cursor.execute("UPDATE accounts SET balance = ? WHERE name = ?", (balance, name))
            conn.commit()
    elif choice == "3":
        break
    else:
        print("Invalid choice. Please try again.")

conn.close()