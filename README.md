# todo-postpone
pro in postponing
import json
import os
from datetime import date, timedelta

DATA_FILE = "todo.json"

# Load existing data
def load_data():
    if os.path.exists(DATA_FILE):
        with open(DATA_FILE, "r") as f:
            return json.load(f)
    return {}

# Save data
def save_data(data):
    with open(DATA_FILE, "w") as f:
        json.dump(data, f, indent=2)

# Get today's list (carry over unfinished from yesterday)
def get_today_list(data):
    today = str(date.today())
    yesterday = str(date.today() - timedelta(days=1))

    if today not in data:
        data[today] = []

        # carry over unfinished from yesterday
        if yesterday in data:
            unfinished = [task for task in data[yesterday] if not task["done"]]
            data[today].extend(unfinished)

    return data[today]

# Add a task
def add_task(data, task):
    today = str(date.today())
    data[today].append({"task": task, "done": False})

# Mark task as done
def mark_done(data, index):
    today = str(date.today())
    if 0 <= index < len(data[today]):
        data[today][index]["done"] = True

# Display tasks
def show_tasks(data):
    today = str(date.today())
    print(f"\nTo-Do List for {today}:")
    for i, task in enumerate(data[today]):
        status = "✅" if task["done"] else "❌"
        print(f"{i+1}. {task['task']} {status}")


def main():
    data = load_data()
    today_list = get_today_list(data)

    while True:
        show_tasks(data)
        print("\nOptions:")
        print("1. Add Task")
        print("2. Mark Task Done")
        print("3. Exit")

        choice = input("Enter choice: ").strip()
        if choice == "1":
            task = input("Enter task: ")
            add_task(data, task)
        elif choice == "2":
            index = int(input("Enter task number: ")) - 1
            mark_done(data, index)
        elif choice == "3":
            break
        else:
            print("Invalid choice!")

    save_data(data)


if __name__ == "__main__":
    main()
