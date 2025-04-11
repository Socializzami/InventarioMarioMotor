<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Inventario Avanzato</title>
  <style>
    :root {
      --primary: #2c3e50;
      --secondary: #ecf0f1;
      --accent: #3498db;
      --danger: #e74c3c;
      --bg: #f8f9fa;
      --text: #2d3436;
    }
    * {
      box-sizing: border-box;
    }
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      margin: 0;
      padding: 2rem;
      background-color: var(--bg);
      color: var(--text);
      animation: fadeIn 0.5s ease;
    }
    @keyframes fadeIn {
      from { opacity: 0; }
      to { opacity: 1; }
    }
    h1 {
      text-align: center;
      color: var(--primary);
    }
    .stats {
      display: flex;
      justify-content: space-around;
      margin-top: 1rem;
      gap: 1rem;
      flex-wrap: wrap;
    }
    .stat {
      background-color: var(--secondary);
      padding: 1rem;
      border-radius: 8px;
      width: 30%;
      text-align: center;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
      transition: transform 0.3s ease;
    }
    .stat:hover {
      transform: scale(1.03);
    }
    input, button, select {
      padding: 10px;
      margin: 5px 0;
      width: 100%;
      border: 1px solid #ccc;
      border-radius: 4px;
      transition: all 0.3s ease;
      font-size: 1rem;
    }
    input:focus, select:focus {
      border-color: var(--accent);
      outline: none;
    }
    button {
      background-color: var(--accent);
      color: white;
      font-weight: bold;
      cursor: pointer;
    }
    button:hover {
      background-color: #2980b9;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 1rem;
      background-color: white;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
    th, td {
      padding: 12px;
      border: 1px solid #ddd;
      text-align: left;
    }
    th {
      background-color: var(--accent);
      color: white;
    }
    tr:hover {
      background-color: #f1f1f1;
    }
    form {
      background-color: white;
      padding: 1rem;
      margin-top: 2rem;
      border-radius: 8px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
    .actions button {
      margin-right: 5px;
      width: auto;
    }
    .top-bar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 1rem;
      flex-wrap: wrap;
      gap: 1rem;
    }
    .top-controls {
      display: flex;
      gap: 10px;
    }
    #search {
      width: 300px;
    }
    #login-screen {
      max-width: 400px;
      margin: 5rem auto;
      padding: 2rem;
      background-color: white;
      border-radius: 8px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.15);
    }
    #main-content {
      display: none;
      animation: fadeIn 1s ease;
    }
    #login-screen h2 {
      text-align: center;
      color: var(--primary);
    }
    #tutorial-modal {
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      background: rgba(0,0,0,0.5);
      display: none;
      justify-content: center;
      align-items: center;
      z-index: 1000;
    }
    #tutorial-box {
      background: white;
      padding: 2rem;
      border-radius: 10px;
      max-width: 600px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.3);
    }
    #tutorial-box h2 {
      margin-top: 0;
    }
  </style>
</head>
<body>
  <div id="login-screen">
    <h2>Login</h2>
    <input type="text" id="username" placeholder="Username">
    <input type="password" id="password" placeholder="Password">
    <button onclick="login()">Accedi</button>
    <p id="login-error" style="color: red;"></p>
  </div>

  <div id="tutorial-modal">
    <div id="tutorial-box">
      <h2>Benvenuto nell'inventario</h2>
      <p>
        Qui puoi:
        <ul>
          <li>Aggiungere nuovi oggetti compilando il modulo</li>
          <li>Cercare elementi usando la barra di ricerca</li>
          <li>Modificare o eliminare ogni voce</li>
          <li>Esportare l'intero inventario in CSV</li>
          <li>Effettuare il logout in qualsiasi momento</li>
        </ul>
      </p>
      <button onclick="closeTutorial()">Ho capito</button>
    </div>
  </div>

  <div id="main-content">
    <h1>Inventario Avanzato</h1>

    <div class="top-bar">
      <input type="text" id="search" placeholder="Cerca...">
      <div class="top-controls">
        <button onclick="exportCSV()">Esporta CSV</button>
        <button onclick="logout()">Logout</button>
      </div>
    </div>

    <form id="item-form">
      <input type="text" id="name" placeholder="Nome" required>
      <input type="text" id="category" placeholder="Categoria" required>
      <input type="number" id="quantity" placeholder="Quantità" required>
      <input type="number" id="price" placeholder="Prezzo per unità">
      <input type="date" id="purchaseDate" placeholder="Data di acquisto">
      <input type="text" id="description" placeholder="Descrizione">
      <button type="submit">Aggiungi oggetto</button>
    </form>

    <div class="stats">
      <div class="stat"><strong>Totale articoli:</strong> <span id="total-items">0</span></div>
      <div class="stat"><strong>Quantità totale:</strong> <span id="total-quantity">0</span></div>
      <div class="stat"><strong>Valore totale (€):</strong> <span id="total-value">0.00</span></div>
    </div>

    <table id="inventory-table">
      <thead>
        <tr>
          <th>Nome</th>
          <th>Categoria</th>
          <th>Quantità</th>
          <th>Prezzo</th>
          <th>Valore Totale</th>
          <th>Data Acquisto</th>
          <th>Descrizione</th>
          <th>Azioni</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>

  <script>
    let inventory = JSON.parse(localStorage.getItem('inventory')) || [];

    function saveInventory() {
      localStorage.setItem('inventory', JSON.stringify(inventory));
    }

    function renderTable(filter = '') {
      const tbody = document.querySelector('#inventory-table tbody');
      tbody.innerHTML = '';
      let totalItems = 0, totalQty = 0, totalVal = 0;

      inventory.forEach((item, index) => {
        if (filter && !JSON.stringify(item).toLowerCase().includes(filter.toLowerCase())) return;
        const row = document.createElement('tr');
        const value = (item.quantity * item.price).toFixed(2);
        row.innerHTML = `
          <td>${item.name}</td>
          <td>${item.category}</td>
          <td>${item.quantity}</td>
          <td>€ ${item.price}</td>
          <td>€ ${value}</td>
          <td>${item.purchaseDate}</td>
          <td>${item.description}</td>
          <td class="actions">
            <button onclick="editItem(${index})">Modifica</button>
            <button onclick="deleteItem(${index})">Elimina</button>
          </td>
        `;
        tbody.appendChild(row);
        totalItems++;
        totalQty += Number(item.quantity);
        totalVal += Number(item.quantity * item.price);
      });

      document.getElementById('total-items').textContent = totalItems;
      document.getElementById('total-quantity').textContent = totalQty;
      document.getElementById('total-value').textContent = totalVal.toFixed(2);
    }

    function deleteItem(index) {
      if (confirm('Sei sicuro di voler eliminare questo oggetto?')) {
        inventory.splice(index, 1);
        saveInventory();
        renderTable();
      }
    }

    function editItem(index) {
      const item = inventory[index];
      document.getElementById('name').value = item.name;
      document.getElementById('category').value = item.category;
      document.getElementById('quantity').value = item.quantity;
      document.getElementById('price').value = item.price;
      document.getElementById('purchaseDate').value = item.purchaseDate;
      document.getElementById('description').value = item.description;
      inventory.splice(index, 1);
      renderTable();
    }

    document.getElementById('item-form').addEventListener('submit', function(e) {
      e.preventDefault();
      const newItem = {
        name: document.getElementById('name').value,
        category: document.getElementById('category').value,
        quantity: parseInt(document.getElementById('quantity').value),
        price: parseFloat(document.getElementById('price').value || 0),
        purchaseDate: document.getElementById('purchaseDate').value,
        description: document.getElementById('description').value
      };
      inventory.push(newItem);
      saveInventory();
      renderTable();
      this.reset();
    });

    document.getElementById('search').addEventListener('input', (e) => {
      renderTable(e.target.value);
    });

    function exportCSV() {
      const rows = [
        ['Nome','Categoria','Quantità','Prezzo','Valore Totale','Data Acquisto','Descrizione']
      ];
      inventory.forEach(item => {
        rows.push([
          item.name,
          item.category,
          item.quantity,
          item.price,
          (item.quantity * item.price).toFixed(2),
          item.purchaseDate,
          item.description
        ]);
      });
      const csvContent = 'data:text/csv;charset=utf-8,' + rows.map(e => e.join(',')).join('\n');
      const encodedUri = encodeURI(csvContent);
      const link = document.createElement('a');
      link.setAttribute('href', encodedUri);
      link.setAttribute('download', 'inventario.csv');
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }

    function login() {
      const username = document.getElementById('username').value;
      const password = document.getElementById('password').value;
      const errorBox = document.getElementById('login-error');
      if (username === 'Admin' && password === 'MarioMelisi22') {
        document.getElementById('login-screen').style.display = 'none';
        document.getElementById('main-content').style.display = 'block';
        document.getElementById('tutorial-modal').style.display = 'flex';
        renderTable();
      } else {
        errorBox.textContent = 'Credenziali errate';
      }
    }

    function logout() {
      document.getElementById('main-content').style.display = 'none';
      document.getElementById('login-screen').style.display = 'block';
      document.getElementById('username').value = '';
      document.getElementById('password').value = '';
    }

    function closeTutorial() {
      document.getElementById('tutorial-modal').style.display = 'none';
    }
  </script>
</body>
</html>
