<!DOCTYPE html>
<html lang="ro">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Monitorizare produse</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0; padding: 0;
      background: #f8f9fa;
    }
    header {
      background: #007bff;
      color: #fff;
      padding: 1rem;
      text-align: center;
    }
    main {
      padding: 1rem;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 1rem;
    }
    th, td {
      border: 1px solid #ddd;
      padding: 0.5rem;
      text-align: left;
    }
    th {
      background-color: #007bff;
      color: white;
    }
    input, button {
      margin: 0.5rem 0;
      padding: 0.5rem;
      font-size: 1rem;
    }
    .scanner {
      margin-top: 1rem;
    }
    @media (max-width: 600px) {
      table, thead, tbody, th, td, tr {
        display: block;
      }
      th {
        text-align: left;
      }
    }
  </style>
</head>
<body>
  <header>
    <h1>Monitorizare produse</h1>
  </header>
  <main>
    <div>
      <label>Denumire produs:
        <input type="text" id="numeProdus" placeholder="ex: Mere"/>
      </label><br/>
      <label>Cantitate:
        <input type="number" id="cantitateProdus" min="1" />
      </label><br/>
      <button onclick="adaugaProdus()">Adaugă produs</button>
    </div>

    <div>
      <input type="text" id="cautare" placeholder="Caută după nume..." onkeyup="cautaProdus()" />
    </div>

    <table id="tabelProduse">
      <thead>
        <tr>
          <th>Produs</th>
          <th>Cantitate</th>
        </tr>
      </thead>
      <tbody>
      </tbody>
    </table>

    <div class="scanner">
      <button onclick="pornesteScanare()">Scanează cod de bare</button>
      <div id="reader" style="width: 300px;"></div>
    </div>
  </main>

  <!-- Html5-qrcode CDN -->
  <script src="https://unpkg.com/html5-qrcode"></script>
  <script>
    const produse = [];

    function adaugaProdus() {
      const nume = document.getElementById('numeProdus').value.trim();
      const cant = document.getElementById('cantitateProdus').value;
      if (nume && cant > 0) {
        produse.push({ nume, cant });
        afiseazaProduse();
      }
      document.getElementById('numeProdus').value = "";
      document.getElementById('cantitateProdus').value = "";
    }

    function afiseazaProduse() {
      const tbody = document.querySelector('#tabelProduse tbody');
      tbody.innerHTML = "";
      produse.forEach(prod => {
        const row = document.createElement('tr');
        row.innerHTML = `<td>${prod.nume}</td><td>${prod.cant}</td>`;
        tbody.appendChild(row);
      });
    }

    function cautaProdus() {
      const filtrat = document.getElementById('cautare').value.toLowerCase();
      const tbody = document.querySelector('#tabelProduse tbody');
      tbody.innerHTML = "";
      produse
        .filter(p => p.nume.toLowerCase().includes(filtrat))
        .forEach(prod => {
          const row = document.createElement('tr');
          row.innerHTML = `<td>${prod.nume}</td><td>${prod.cant}</td>`;
          tbody.appendChild(row);
        });
    }

    function pornesteScanare() {
      const html5QrCode = new Html5Qrcode("reader");
      Html5Qrcode.getCameras().then(cameras => {
        if (cameras && cameras.length) {
          html5QrCode.start(
            cameras[0].id,
            { fps: 10, qrbox: 250 },
            (decodedText, decodedResult) => {
              document.getElementById('numeProdus').value = decodedText;
              html5QrCode.stop();
            },
            error => {
              console.warn(error);
            })
            .catch(err => {
              console.error(err);
            });
        }
      });
    }
  </script>
</body>
</html>
