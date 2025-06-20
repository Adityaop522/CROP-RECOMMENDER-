# CROP-RECOMMENDER-
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Crop Recommender</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      color: #333;
      background-size: cover;
      background-repeat: no-repeat;
      background-position: center;
      padding: 20px;
    }
    .container {
      background-color: rgba(255, 255, 255, 0.9);
      padding: 20px;
      border-radius: 12px;
      max-width: 600px;
      margin: auto;
    }
    .error {
      color: red;
    }
    table {
      width: 100%;
      margin-top: 20px;
      border-collapse: collapse;
    }
    table, th, td {
      border: 1px solid #999;
    }
    th, td {
      padding: 10px;
      text-align: center;
    }
  </style>
</head>
<body>
  <input type="file" id="soilFile" accept=".txt"> Load Soil Types<br><br>
  <input type="file" id="plantsFile" accept=".txt"> Load Crop List<br><br>
  <input type="file" id="bgFile" accept="image/*"> Load Background Image<br><br>

  <div class="container">
    <h2>Crop Recommender</h2>

    <label>Humidity (%):</label><br>
    <input type="number" id="humidity" min="0" max="100"><br><br>

    <label>Temperature (°C):</label><br>
    <input type="number" id="temperature"><br><br>

    <label>Soil Temperature (°C):</label><br>
    <input type="number" id="soilTemp"><br>
    <p class="error" id="tempError"></p>

    <label>Soil Type:</label><br>
    <select id="soilType">
      <option value="">-- Load Soil File First --</option>
    </select><br><br>

    <button onclick="recommendCrops()">Recommend Crops</button>

    <div id="result"></div>
  </div>

  <script>
    let soilTypes = [];
    let cropList = [];

    document.getElementById('soilFile').addEventListener('change', function (e) {
      const reader = new FileReader();
      reader.onload = function () {
        soilTypes = reader.result.split(/\r?\n/).filter(Boolean);
        const select = document.getElementById('soilType');
        select.innerHTML = '';
        soilTypes.forEach(type => {
          const opt = document.createElement('option');
          opt.value = type;
          opt.textContent = type;
          select.appendChild(opt);
        });
      };
      reader.readAsText(e.target.files[0]);
    });

    document.getElementById('plantsFile').addEventListener('change', function (e) {
      const reader = new FileReader();
      reader.onload = function () {
        cropList = reader.result.split(/\r?\n/).filter(Boolean);
      };
      reader.readAsText(e.target.files[0]);
    });

    document.getElementById('bgFile').addEventListener('change', function (e) {
      const file = e.target.files[0];
      const reader = new FileReader();
      reader.onload = function () {
        document.body.style.backgroundImage = `url(${reader.result})`;
      };
      reader.readAsDataURL(file);
    });

    function recommendCrops() {
      const humidity = parseFloat(document.getElementById('humidity').value);
      const temperature = parseFloat(document.getElementById('temperature').value);
      const soilTemp = parseFloat(document.getElementById('soilTemp').value);
      const soilType = document.getElementById('soilType').value;
      const tempError = document.getElementById('tempError');
      const resultDiv = document.getElementById('result');
      resultDiv.innerHTML = '';
      tempError.textContent = '';

      if (isNaN(soilTemp) || soilTemp < 3 || soilTemp > 35) {
        tempError.textContent = "The Limit Is 3 degree Celsius to 35 degree Celsius";
        return;
      }

      if (!soilType) {
        alert("Please select a soil type.");
        return;
      }

      if (cropList.length < 3) {
        alert("Please upload a valid crops list with more than 3 crops.");
        return;
      }

      // Simple random selection (or insert real logic here)
      const recommended = cropList.sort(() => 0.5 - Math.random()).slice(0, 5);

      let html = '<h3>Recommended Crops:</h3>';
      html += '<table><tr><th>#</th><th>Crop Name</th></tr>';
      recommended.forEach((crop, index) => {
        html += `<tr><td>${index + 1}</td><td>${crop}</td></tr>`;
      });
      html += '</table>';
      resultDiv.innerHTML = html;
    }
  </script>
</body>
</html>
