# Windshield-Estimate-V3
Quick glass estimator
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Windshield Estimate Calculator</title>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
body {
  font-family: Arial, sans-serif;
  background: #f4f4f4;
  padding: 20px;
}
.container {
  background: #fff;
  padding: 20px;
  border-radius: 8px;
  max-width: 420px;
  margin: auto;
}
label {
  font-weight: bold;
}
input, select {
  width: 100%;
  padding: 8px;
  margin: 8px 0 15px;
}
button {
  width: 100%;
  padding: 12px;
  background: #0b5ed7;
  color: #fff;
  border: none;
  font-size: 16px;
  border-radius: 6px;
}
.result {
  margin-top: 15px;
  font-size: 18px;
  font-weight: bold;
}
.breakdown {
  margin-top: 10px;
  font-size: 14px;
}
.small {
  font-size: 12px;
  color: #555;
}
</style>
</head>

<body>
<div class="container">
  <h2>Windshield Estimate</h2>

  <label>Glass Cost ($)</label>
  <input type="number" id="glassCost" placeholder="Enter glass cost">

  <label>Molding Cost ($)</label>
  <input type="number" id="moldingCost" placeholder="0 if none">

  <label>Rain Sensor Rebuild Kit</label>
  <select id="rainSensor">
    <option value="0">No</option>
    <option value="17">Yes ($17)</option>
  </select>

<label>ADAS Calibration</label>
<select id="adas">
  <option value="0">None</option>
  <option value="315">Static ($315)</option>
  <option value="200">Dynamic ($200)</option>
  <option value="515">Dual – Static + Dynamic ($515)</option>
</select>

  <hr>

  <label>Additional Part (optional)</label>
  <input type="text" id="extraPartName" placeholder="Part description (ex: cowl, clips)">
  <input type="number" id="extraPartCost" placeholder="Part cost $">

  <button onclick="calculate()">Calculate Estimate</button>

  <div class="result" id="total"></div>
  <div class="breakdown" id="breakdown"></div>
</div>

<script>
function calculate() {
  const glass = Number(document.getElementById("glassCost").value) || 0;
  const molding = Number(document.getElementById("moldingCost").value) || 0;
  const rainSensor = Number(document.getElementById("rainSensor").value);
  const adas = Number(document.getElementById("adas").value);

  const extraPartName = document.getElementById("extraPartName").value;
  const extraPartCost = Number(document.getElementById("extraPartCost").value) || 0;

  // Markups
  const glassWithMarkup = glass * 1.25;
  const moldingWithMarkup = molding * 1.25;
  const extraPartWithMarkup = extraPartCost * 1.25;

  const labor = 220;
  const materials = 50;

  // TAXABLE PARTS ONLY
  const taxableParts =
    glassWithMarkup +
    moldingWithMarkup +
    rainSensor +
    materials +
    extraPartWithMarkup;

  const tax = taxableParts * 0.0825;

  // NON-TAXABLE
  const nonTaxable =
    labor +
    adas;

  const subtotal = taxableParts + nonTaxable;
  const totalWithTax = subtotal + tax;

  // Always add 4% payment fee
  const paymentFee = totalWithTax * 0.04;
  const finalTotal = totalWithTax + paymentFee;

  document.getElementById("total").innerHTML =
    "Estimated Total: $" + finalTotal.toFixed(2);

  let extraPartLine = "";
  if (extraPartCost > 0) {
    extraPartLine = `${extraPartName || "Additional Part"} (25% markup): $${extraPartWithMarkup.toFixed(2)}<br>`;
  }

  document.getElementById("breakdown").innerHTML = `
    Glass (25% markup): $${glassWithMarkup.toFixed(2)}<br>
    Molding (25% markup): $${moldingWithMarkup.toFixed(2)}<br>
    Rain Sensor Kit: $${rainSensor.toFixed(2)}<br>
    Materials: $50.00<br>
    ${extraPartLine}
    Labor (non-taxable): $220.00<br>
    ADAS (non-taxable): $${adas.toFixed(2)}<br>
    Tax (8.25% on parts only): $${tax.toFixed(2)}<br>
    Payment Fee (4%): $${paymentFee.toFixed(2)}
  `;
}
</script>
</body>
</html>
