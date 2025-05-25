const fs = require("fs");
const path = require("path");
const readline = require("readline");

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

const filePath = path.join(__dirname, "tasks.json");

function loadTasks() {
  if (!fs.existsSync(filePath)) {
    return [];
  }
  const data = fs.readFileSync(filePath, "utf8");
  return JSON.parse(data);
}

function saveTasks(tasks) {
  fs.writeFileSync(filePath, JSON.stringify(tasks, null, 2), "utf8");
}

function addTask() {
  rl.question("Task description: ", (desc) => {
    const tasks = loadTasks();
    tasks.push({ description: desc, completed: false });
    saveTasks(tasks);
    console.log("Task added.");
    rl.close();
  });
}

function viewTasks() {
  const tasks = loadTasks();
  if (tasks.length === 0) {
    console.log("No tasks found.");
  } else {
    tasks.forEach((task, i) => {
      const status = task.completed ? "✓" : " ";
      console.log(`${i + 1}. [${status}] ${task.description}`);
    });
  }
}

function markComplete() {
  const tasks = loadTasks();
  if (tasks.length === 0) {
    console.log("No tasks to mark.");
    rl.close();
    return;
  }

  viewTasks();
  rl.question("Task number to mark complete: ", (num) => {
    const index = parseInt(num) - 1;
    if (tasks[index]) {
      tasks[index].completed = true;
      saveTasks(tasks);
      console.log("Task marked as complete.");
    } else {
      console.log("Invalid number.");
    }
    rl.close();
  });
}

function removeTask() {
  const tasks = loadTasks();
  if (tasks.length === 0) {
    console.log("No tasks to remove.");
    rl.close();
    return;
  }

  viewTasks();
  rl.question("Task number to remove: ", (num) => {
    const index = parseInt(num) - 1;
    if (tasks[index]) {
      tasks.splice(index, 1);
      saveTasks(tasks);
      console.log("Task removed.");
    } else {
      console.log("Invalid number.");
    }
    rl.close();
  });
}

console.log("Choose an option:");
console.log("1. Add Task");
console.log("2. View Tasks");
console.log("3. Mark Task as Complete");
console.log("4. Remove Task");
console.log("5. Exit");

rl.question("Enter number: ", (answer) => {
  switch (answer.trim()) {
    case "1":
      addTask();
      break;
    case "2":
      viewTasks();
      break;
    case "3":
      markComplete();
      break;
    case "4":
      removeTask();
      break;
    case "5":
      rl.close();
      break;
    default:
      console.log("Invalid option.");
      rl.close();
  }
});
