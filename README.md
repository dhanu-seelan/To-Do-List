Ex03 To-Do List using JavaScript\
Name : Danaseelan G

Reg No : 212225040053

Date: 17 - 11 -2026.

AIM
To create a To-do Application with all features using JavaScript.

ALGORITHM
STEP 1
Build the HTML structure (index.html).
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>To-Do List</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="container">
    <h1>My To-Do List</h1>

    <div class="input-row">
      <input type="text" id="taskInput" placeholder="Add a new task..." maxlength="100">
      <button id="addBtn">Add</button>
    </div>

    <div class="filters">
      <button class="filter active" data-filter="all">All</button>
      <button class="filter" data-filter="active">Active</button>
      <button class="filter" data-filter="completed">Completed</button>
    </div>

    <ul id="taskList"></ul>
    <p id="emptyMsg" class="empty">No tasks to show.</p>

    <div class="footer">
      <span id="counter">0 tasks left</span>
      <button id="clearBtn">Clear Completed</button>
    </div>
  </div>

  <script src="script.js"></script>
</body>
</html>
```

STEP 2
Style the App (style.css).
```
* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: Arial, sans-serif;
  background: linear-gradient(135deg, #667eea, #764ba2);
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 40px 15px;
}

.container {
  background: #fff;
  width: 100%;
  max-width: 480px;
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.25);
}

h1 { text-align: center; color: #333; margin-bottom: 20px; }

.input-row { display: flex; gap: 8px; margin-bottom: 15px; }

#taskInput {
  flex: 1;
  padding: 10px 12px;
  border: 2px solid #ddd;
  border-radius: 8px;
  font-size: 16px;
}
#taskInput:focus { outline: none; border-color: #667eea; }

button {
  cursor: pointer;
  border: none;
  border-radius: 8px;
  font-size: 14px;
  transition: 0.2s;
}

#addBtn { background: #667eea; color: #fff; padding: 10px 18px; }
#addBtn:hover { background: #5568d3; }

.filters { display: flex; justify-content: center; gap: 8px; margin-bottom: 15px; }
.filter { background: #eee; padding: 6px 14px; color: #444; }
.filter.active { background: #667eea; color: #fff; }

ul { list-style: none; }

li {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px;
  border-bottom: 1px solid #eee;
}

li span { flex: 1; word-break: break-word; cursor: pointer; }
li.done span { text-decoration: line-through; color: #999; }

li input[type="checkbox"] { width: 18px; height: 18px; }
li input.edit-input { flex: 1; padding: 4px 6px; font-size: 16px; }

.edit-btn { background: #f0ad4e; color: #fff; padding: 5px 10px; }
.del-btn { background: #e74c3c; color: #fff; padding: 5px 10px; }

.empty { text-align: center; color: #999; padding: 15px; display: none; }

.footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 15px;
  color: #555;
  font-size: 14px;
}
#clearBtn { background: #888; color: #fff; padding: 6px 12px; }
#clearBtn:hover { background: #666; }

@media (max-width: 400px) {
  .container { padding: 16px; }
}
```

STEP 3
Plan the features the To-Do App should have.

STEP 4
Create a To-do application using Javascript.
```
const taskInput = document.getElementById("taskInput");
const addBtn = document.getElementById("addBtn");
const taskList = document.getElementById("taskList");
const emptyMsg = document.getElementById("emptyMsg");
const counter = document.getElementById("counter");
const clearBtn = document.getElementById("clearBtn");
const filterBtns = document.querySelectorAll(".filter");

let tasks = JSON.parse(localStorage.getItem("tasks")) || [];
let currentFilter = "all";

function saveTasks() {
  localStorage.setItem("tasks", JSON.stringify(tasks));
}

function addTask() {
  const text = taskInput.value.trim();
  if (text === "") {
    alert("Please enter a task!");
    return;
  }
  tasks.push({ id: Date.now(), text: text, completed: false });
  taskInput.value = "";
  saveTasks();
  renderTasks();
}

function toggleTask(id) {
  tasks = tasks.map((t) =>
    t.id === id ? { ...t, completed: !t.completed } : t,
  );
  saveTasks();
  renderTasks();
}

function deleteTask(id) {
  tasks = tasks.filter((t) => t.id !== id);
  saveTasks();
  renderTasks();
}

function editTask(id, li) {
  const task = tasks.find((t) => t.id === id);
  const span = li.querySelector("span");

  const input = document.createElement("input");
  input.type = "text";
  input.value = task.text;
  input.className = "edit-input";
  li.replaceChild(input, span);
  input.focus();

  function save() {
    const newText = input.value.trim();
    if (newText !== "") task.text = newText;
    saveTasks();
    renderTasks();
  }
  input.addEventListener("blur", save);
  input.addEventListener("keypress", (e) => {
    if (e.key === "Enter") input.blur();
  });
}

function clearCompleted() {
  tasks = tasks.filter((t) => !t.completed);
  saveTasks();
  renderTasks();
}

function renderTasks() {
  taskList.innerHTML = "";

  const visible = tasks.filter((t) => {
    if (currentFilter === "active") return !t.completed;
    if (currentFilter === "completed") return t.completed;
    return true;
  });

  visible.forEach((task) => {
    const li = document.createElement("li");
    if (task.completed) li.classList.add("done");

    const checkbox = document.createElement("input");
    checkbox.type = "checkbox";
    checkbox.checked = task.completed;
    checkbox.addEventListener("change", () => toggleTask(task.id));

    const span = document.createElement("span");
    span.textContent = task.text;
    span.addEventListener("dblclick", () => editTask(task.id, li));

    const editBtn = document.createElement("button");
    editBtn.textContent = "Edit";
    editBtn.className = "edit-btn";
    editBtn.addEventListener("click", () => editTask(task.id, li));

    const delBtn = document.createElement("button");
    delBtn.textContent = "Delete";
    delBtn.className = "del-btn";
    delBtn.addEventListener("click", () => deleteTask(task.id));

    li.append(checkbox, span, editBtn, delBtn);
    taskList.appendChild(li);
  });

  emptyMsg.style.display = visible.length === 0 ? "block" : "none";

  const left = tasks.filter((t) => !t.completed).length;
  counter.textContent = left + (left === 1 ? " task left" : " tasks left");
}

addBtn.addEventListener("click", addTask);
taskInput.addEventListener("keypress", (e) => {
  if (e.key === "Enter") addTask();
});
clearBtn.addEventListener("click", clearCompleted);

filterBtns.forEach((btn) => {
  btn.addEventListener("click", () => {
    filterBtns.forEach((b) => b.classList.remove("active"));
    btn.classList.add("active");
    currentFilter = btn.dataset.filter;
    renderTasks();
  });
});

renderTasks();

```

STEP 5
Add functionalities.

STEP 6
Test the App.

STEP 7
Open the HTML file in a browser to check layout and functionality.

STEP 8
Fix styling issues and refine content placement.

STEP 9
Deploy the website.

STEP 10
Upload to GitHub Pages for free hosting.

PROGRAM
OUTPUT
<img width="1902" height="908" alt="Screenshot 2026-11-16 211726" src="https://github.com/user-attachments/assets/414c9418-42de-4570-bba8-787285913ba9" />

RESULT
The program for creating To-do list using JavaScript is executed successfully.
