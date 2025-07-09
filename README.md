
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Ma Checklist</title>
  <style>
    body { font-family: sans-serif; background: #f6f6f6; margin: 0; padding: 0;}
    .container { max-width: 400px; margin: 40px auto; background: #fff; border-radius: 16px; box-shadow: 0 2px 10px #0001; padding: 2em; }
    h1 { text-align: center; }
    form { display: flex; margin-bottom: 1em; }
    input[type="text"] { flex: 1; padding: 0.5em; border: 1px solid #bbb; border-radius: 8px; }
    button { margin-left: 8px; border: none; background: #4caf50; color: #fff; padding: 0 1em; border-radius: 8px; font-weight: bold; cursor: pointer; }
    ul { list-style: none; padding: 0; }
    li { display: flex; align-items: center; margin-bottom: 8px; background: #f2f2f2; border-radius: 8px; padding: 8px;}
    li.checked label { text-decoration: line-through; color: #888;}
    label { flex: 1; cursor: pointer; }
    .actions { display: flex; gap: 4px; }
    .edit, .delete { background: none; border: none; cursor: pointer; color: #666; font-size: 1.1em; }
    .edit:hover { color: #2196f3; }
    .delete:hover { color: #f44336; }
  </style>
</head>
<body>
  <div class="container">
    <h1>Ma Checklist</h1>
    <form id="addForm">
      <input type="text" id="newTask" placeholder="Ajouter une tâche..." autocomplete="off" required>
      <button type="submit">+</button>
    </form>
    <ul id="tasks"></ul>
  </div>
  <script>
    let tasks = JSON.parse(localStorage.getItem('tasks') || '[]');

    function saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(tasks));
    }

    function renderTasks() {
      const ul = document.getElementById('tasks');
      ul.innerHTML = '';
      tasks.forEach((task, idx) => {
        const li = document.createElement('li');
        if (task.done) li.classList.add('checked');
        li.innerHTML = `
          <input type="checkbox" ${task.done ? 'checked' : ''} onclick="toggleTask(${idx})">
          <label ondblclick="editTask(${idx})">${task.text}</label>
          <span class="actions">
            <button class="edit" title="Modifier" onclick="editTask(${idx})">&#9998;</button>
            <button class="delete" title="Supprimer" onclick="deleteTask(${idx})">&#10006;</button>
          </span>
        `;
        ul.appendChild(li);
      });
    }

    function addTask(text) {
      tasks.push({ text, done: false });
      saveTasks(); renderTasks();
    }

    function deleteTask(idx) {
      if (confirm('Supprimer cette tâche ?')) {
        tasks.splice(idx, 1);
        saveTasks(); renderTasks();
      }
    }

    function toggleTask(idx) {
      tasks[idx].done = !tasks[idx].done;
      saveTasks(); renderTasks();
    }

    function editTask(idx) {
      const nouveau = prompt("Modifier la tâche :", tasks[idx].text);
      if (nouveau !== null && nouveau.trim() !== "") {
        tasks[idx].text = nouveau.trim();
        saveTasks(); renderTasks();
      }
    }

    document.getElementById('addForm').addEventListener('submit', function(e) {
      e.preventDefault();
      const input = document.getElementById('newTask');
      addTask(input.value.trim());
      input.value = '';
      input.focus();
    });

    renderTasks();
    // Pour accès des fonctions dans HTML
    window.toggleTask = toggleTask;
    window.deleteTask = deleteTask;
    window.editTask = editTask;
  </script>
</body>
</html>
