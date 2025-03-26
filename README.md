import tkinter as tk
from tkinter import messagebox
import json
import os

# File to store tasks
TASKS_FILE = "tasks.json"

class ToDoApp:
    def __init__(self, root):  # Fixed constructor method
        self.root = root
        self.root.title("To-Do List")
        self.root.geometry("400x500")

        self.task_list = []
        self.load_tasks()

        self.task_entry = tk.Entry(root, width=40)
        self.task_entry.pack(pady=10)

        self.add_button = tk.Button(root, text="Add Task", command=self.add_task)
        self.add_button.pack()

        self.listbox = tk.Listbox(root, width=50, height=15)
        self.listbox.pack(pady=10)
        self.populate_listbox()

        self.complete_button = tk.Button(root, text="Mark as Complete", command=self.mark_complete)
        self.complete_button.pack()

        self.delete_button = tk.Button(root, text="Delete Task", command=self.delete_task)
        self.delete_button.pack()

        self.clear_button = tk.Button(root, text="Clear All", command=self.clear_tasks)
        self.clear_button.pack()

    def add_task(self):
        task = self.task_entry.get().strip()
        if task:
            self.task_list.append({"task": task, "completed": False})
            self.listbox.insert(tk.END, task)
            self.task_entry.delete(0, tk.END)
            self.save_tasks()
        else:
            messagebox.showwarning("Warning", "Task cannot be empty!")

    def mark_complete(self):
        try:
            index = self.listbox.curselection()[0]
            self.task_list[index]["completed"] = True
            self.listbox.delete(index)
            self.listbox.insert(index, f"✔ {self.task_list[index]['task']}")
            self.save_tasks()
        except IndexError:
            messagebox.showwarning("Warning", "Please select a task to mark as complete!")

    def delete_task(self):
        try:
            index = self.listbox.curselection()[0]
            self.listbox.delete(index)
            del self.task_list[index]
            self.save_tasks()
        except IndexError:
            messagebox.showwarning("Warning", "Please select a task to delete!")

    def clear_tasks(self):
        self.listbox.delete(0, tk.END)
        self.task_list.clear()
        self.save_tasks()

    def save_tasks(self):
        with open(TASKS_FILE, "w") as file:
            json.dump(self.task_list, file)

    def load_tasks(self):
        if os.path.exists(TASKS_FILE):
            with open(TASKS_FILE, "r") as file:
                self.task_list = json.load(file)

    def populate_listbox(self):
        for task in self.task_list:
            display_text = f"✔ {task['task']}" if task["completed"] else task["task"]
            self.listbox.insert(tk.END, display_text)

if __name__ == "__main__":  # Fixed typo in main block
    root = tk.Tk()
    app = ToDoApp(root)
    root.mainloop()
