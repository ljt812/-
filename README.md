import pickle
import os

# 文件名用于存储待办事项列表
TODOS_FILE = 'dolist.pkl'

# 待办事项类
class TodoItem:
    def __init__(self, id, description, completed=False):
        self.id = id
        self.description = description
        self.completed = completed

    def __str__(self):
        return f"{self.id}: {'[x] ' if self.completed else '[ ] '}{self.description}"

# 加载待办事项列表
def load_todos():
    if os.path.exists(TODOS_FILE):
        with open(TODOS_FILE, 'rb') as f:
            return pickle.load(f)
    else:
        return []

# 保存待办事项列表
def save_todos(todos):
    with open(TODOS_FILE, 'wb') as f:
        pickle.dump(todos, f)

# 添加新的待办事项
def add_todo(todos, description):
    # 重置所有待办事项的ID
    for i, todo in enumerate(todos, start=1):
        todo.id = i
    # 添加新的待办事项
    todos.append(TodoItem(len(todos) + 1, description))
    save_todos(todos)

# 显示所有待办事项
def list_todos(todos):
    for todo in todos:
        print(todo)

# 标记待办事项为完成
def mark_todo_completed(todos, todo_id):
    for todo in todos:
        if todo.id == todo_id:
            todo.completed = True
            save_todos(todos)
            return
    print(f"Todo with id {todo_id} not found.")

# 删除已完成的待办事项
def delete_completed_todos(todos):
    todos[:] = [todo for todo in todos if not todo.completed]
    # 重置剩余待办事项的ID
    for i, todo in enumerate(todos, start=1):
        todo.id = i
    save_todos(todos)

# 主程序
def main():
    flag=True
    if os.path.exists(TODOS_FILE) and os.path.getsize(TODOS_FILE) > 0:
        todos = load_todos()
    else:
         flag=False
         print("1: Add a new todo")
         command = input("Enter command: ")
         if command == '1':
            todos = load_todos()
            description = input("Enter todo description: ")
            add_todo(todos, description)
    while True:
        print("\nCommands:")
        print("1: Add a new todo")
        print("2: List all todos")
        print("3: Mark a todo as completed")
        print("4: Delete completed todos")
        print("5: Exit")
        command = input("Enter command: ")
        if command == '1':
            description = input("Enter todo description: ")
            add_todo(todos, description)
        elif command == '2':
            list_todos(todos)
        elif command == '3':
            try:
                todo_id = int(input("Enter todo id to mark as completed: "))
                mark_todo_completed(todos, todo_id)
            except ValueError:
                print("Invalid todo id. Please enter a number.")
        elif command == '4':
            delete_completed_todos(todos)
            print("Completed todos deleted.")
        elif command == '5':
            break
        else:
            print("Invalid command.")

if __name__ == "__main__":
    main()
