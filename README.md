# Coding-Raja-Technologies-Internship
import os
import json
from datetime import datetime

class Task:
    def __init__(self, description, priority, due_date=None, completed=False):
        self.description = description
        self.priority = priority
        self.due_date = due_date
        self.completed = completed

    def __str__(self):
        status = "Completed" if self.completed else "Pending"
        due = f"Due Date: {self.due_date}" if self.due_date else "No Due Date"
        return f"{self.description} ({self.priority}) - {status} - {due}"

class ToDoList:
    def __init__(self):
        self.tasks = []

    def add_task(self, task):
        self.tasks.append(task)

    def remove_task(self, task_description):
        for task in self.tasks:
            if task.description == task_description:
                self.tasks.remove(task)
                return True
        return False

    def mark_task_completed(self, task_description):
        for task in self.tasks:
            if task.description == task_description:
                task.completed = True
                return True
        return False

    def list_tasks(self):
        if not self.tasks:
            print("No tasks.")
        else:
            for task in self.tasks:
                print(task)

    def save_to_file(self, filename):
        with open(filename, 'w') as f:
            json.dump([task.__dict__ for task in self.tasks], f)

    def load_from_file(self, filename):
        if os.path.exists(filename):
            with open(filename, 'r') as f:
                data = json.load(f)
                self.tasks = [Task(**task) for task in data]

def main():
    todo_list = ToDoList()
    filename = "tasks.json"
    todo_list.load_from_file(filename)

    while True:
        print("\nCommand Options:")
        print("1. Add Task")
        print("2. Remove Task")
        print("3. Mark Task as Completed")
        print("4. List Tasks")
        print("5. Save and Exit")

        choice = input("Enter your choice: ")

        if choice == "1":
            description = input("Enter task description: ")
            priority = input("Enter priority (high, medium, low): ")
            due_date_str = input("Enter due date (YYYY-MM-DD), leave blank if none: ")
            due_date = datetime.strptime(due_date_str, '%Y-%m-%d') if due_date_str else None
            task = Task(description, priority, due_date)
            todo_list.add_task(task)
        elif choice == "2":
            description = input("Enter task description to remove: ")
            if not todo_list.remove_task(description):
                print("Task not found.")
        elif choice == "3":
            description = input("Enter task description to mark as completed: ")
            if not todo_list.mark_task_completed(description):
                print("Task not found.")
        elif choice == "4":
            todo_list.list_tasks()
        elif choice == "5":
            todo_list.save_to_file(filename)
            print("Tasks saved. Exiting...")
            break
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    main()

