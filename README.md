<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Interactive Task Manager</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      background: #0f172a;
      color: #e5e7eb;
      display: flex;
      justify-content: center;
      padding: 2rem 1rem;
    }

    .app {
      width: 100%;
      max-width: 480px;
      background: #020617;
      border-radius: 16px;
      padding: 1.5rem 1.4rem;
      box-shadow: 0 18px 45px rgba(0, 0, 0, 0.5);
      border: 1px solid rgba(148, 163, 184, 0.4);
    }

    .app-title {
      text-align: center;
      margin-bottom: 1.2rem;
    }

    .app-title h1 {
      font-size: 1.5rem;
      margin-bottom: 0.3rem;
    }

    .app-title p {
      font-size: 0.85rem;
      color: #9ca3af;
    }

    .task-input-row {
      display: flex;
      gap: 0.6rem;
      margin-bottom: 1rem;
    }

    .task-input-row input {
      flex: 1;
      border-radius: 999px;
      border: 1px solid #4b5563;
      padding: 0.5rem 0.9rem;
      background: #020617;
      color: #e5e7eb;
      font: inherit;
    }

    .task-input-row input:focus {
      outline: none;
      border-color: #38bdf8;
      box-shadow: 0 0 0 1px #38bdf8;
    }

    .task-input-row button {
      border-radius: 999px;
      border: none;
      padding: 0.5rem 0.9rem;
      background: #38bdf8;
      color: #020617;
      font-size: 0.9rem;
      font-weight: 600;
      cursor: pointer;
      transition: transform 0.15s ease, box-shadow 0.15s ease;
      white-space: nowrap;
    }

    .task-input-row button:hover {
      transform: translateY(-1px);
      box-shadow: 0 6px 18px rgba(56, 189, 248, 0.4);
    }

    .task-input-row button:active {
      transform: translateY(0);
      box-shadow: none;
    }

    .empty-state {
      font-size: 0.9rem;
      color: #9ca3af;
      text-align: center;
      padding: 1rem 0.5rem;
      border-radius: 12px;
      border: 1px dashed #4b5563;
      margin-top: 0.5rem;
    }

    ul.task-list {
      list-style: none;
      margin-top: 0.5rem;
      max-height: 340px;
      overflow-y: auto;
      padding-right: 0.2rem;
    }

    .task-item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 0.6rem;
      padding: 0.55rem 0.7rem;
      border-radius: 12px;
      border: 1px solid #1f2933;
      background: #020617;
      margin-bottom: 0.5rem;
      transition: background 0.15s ease, transform 0.1s ease;
    }

    .task-item:hover {
      background: #02091b;
      transform: translateY(-1px);
    }

    .task-left {
      display: flex;
      align-items: center;
      gap: 0.5rem;
      flex: 1;
      min-width: 0;
    }

    .task-left input[type="checkbox"] {
      width: 18px;
      height: 18px;
      cursor: pointer;
    }

    .task-text {
      font-size: 0.95rem;
      word-break: break-word;
    }

    .task-text.completed {
      text-decoration: line-through;
      color: #6b7280;
      opacity: 0.8;
    }

    .task-right {
      display: flex;
      align-items: center;
      gap: 0.4rem;
    }

    .tag {
      font-size: 0.7rem;
      padding: 0.15rem 0.55rem;
      border-radius: 999px;
      border: 1px solid #4b5563;
      color: #9ca3af;
      white-space: nowrap;
    }

    .delete-btn {
      border-radius: 999px;
      border: none;
      padding: 0.25rem 0.5rem;
      background: #ef4444;
      color: white;
      font-size: 0.75rem;
      cursor: pointer;
      transition: opacity 0.15s ease, transform 0.1s ease;
    }

    .delete-btn:hover {
      opacity: 0.85;
      transform: translateY(-1px);
    }

    .counter-row {
      display: flex;
      justify-content: space-between;
      font-size: 0.8rem;
      color: #9ca3af;
      margin-top: 0.6rem;
    }
  </style>
</head>
<body>
  <div class="app">
    <div class="app-title">
      <h1>Task Manager</h1>
      <p>Add tasks, mark them as complete, and delete when done.</p>
    </div>

    <!-- Input Row -->
    <div class="task-input-row">
      <input
        type="text"
        id="taskInput"
        placeholder="Enter a new task..."
      />
      <button id="addTaskBtn">Add</button>
    </div>

    <!-- Counters -->
    <div class="counter-row">
      <span id="totalCount">Total: 0</span>
      <span id="completedCount">Completed: 0</span>
    </div>

    <!-- Empty State -->
    <div id="emptyState" class="empty-state">
      No tasks yet. Add one to get started!
    </div>

    <!-- Task List -->
    <ul id="taskList" class="task-list"></ul>
  </div>

  <script>
    // Get elements
    const taskInput = document.getElementById("taskInput");
    const addTaskBtn = document.getElementById("addTaskBtn");
    const taskList = document.getElementById("taskList");
    const emptyState = document.getElementById("emptyState");
    const totalCountSpan = document.getElementById("totalCount");
    const completedCountSpan = document.getElementById("completedCount");

    // Add task when button is clicked
    addTaskBtn.addEventListener("click", addTask);

    // Add task when "Enter" key is pressed
    taskInput.addEventListener("keyup", function (event) {
      if (event.key === "Enter") {
        addTask();
      }
    });

    function addTask() {
      const text = taskInput.value.trim();

      // Prevent empty tasks
      if (text === "") {
        alert("Please enter a task.");
        return;
      }

      // Create list item
      const li = document.createElement("li");
      li.className = "task-item";

      // Left side: checkbox + text
      const leftDiv = document.createElement("div");
      leftDiv.className = "task-left";

      const checkbox = document.createElement("input");
      checkbox.type = "checkbox";

      const spanText = document.createElement("span");
      spanText.className = "task-text";
      spanText.textContent = text;

      leftDiv.appendChild(checkbox);
      leftDiv.appendChild(spanText);

      // Right side: tag + delete button
      const rightDiv = document.createElement("div");
      rightDiv.className = "task-right";

      const tag = document.createElement("span");
      tag.className = "tag";
      tag.textContent = "pending";

      const deleteBtn = document.createElement("button");
      deleteBtn.className = "delete-btn";
      deleteBtn.textContent = "Delete";

      rightDiv.appendChild(tag);
      rightDiv.appendChild(deleteBtn);

      // Add left + right to list item
      li.appendChild(leftDiv);
      li.appendChild(rightDiv);

      // Add item to list
      taskList.appendChild(li);

      // Clear input
      taskInput.value = "";
      taskInput.focus();

      // Hide empty state if needed and update counter
      updateEmptyState();
      updateCounters();

      // Mark complete / incomplete
      checkbox.addEventListener("change", function () {
        if (checkbox.checked) {
          spanText.classList.add("completed");
          tag.textContent = "done";
        } else {
          spanText.classList.remove("completed");
          tag.textContent = "pending";
        }
        updateCounters();
      });

      // Delete task
      deleteBtn.addEventListener("click", function () {
        li.remove();
        updateEmptyState();
        updateCounters();
      });
    }

    function updateEmptyState() {
      if (taskList.children.length === 0) {
        emptyState.style.display = "block";
      } else {
        emptyState.style.display = "none";
      }
    }

    function updateCounters() {
      const total = taskList.children.length;
      let completed = 0;

      // Count completed tasks
      Array.from(taskList.children).forEach((li) => {
        const checkbox = li.querySelector('input[type="checkbox"]');
        if (checkbox.checked) {
          completed++;
        }
      });

      totalCountSpan.textContent = "Total: " + total;
      completedCountSpan.textContent = "Completed: " + completed;
    }
  </script>
</body>
</html>
