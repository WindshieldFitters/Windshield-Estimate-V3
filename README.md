# TJ's Glass Estimator

<html lang="en">
<head>
<meta charset="UTF-8">
<title>Glass Estimate Calculator</title>
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
  font-size: 16px;
}

button {
  width: 100%;
  padding: 12px;
  background: #0b5ed7;
  color: #fff;
  border: none;
  font-size: 16px;
  border-radius: 6px;
  cursor: pointer;
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

/* MOBILE TOGGLES */
.toggle {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 15px;
  font-size: 16px;
}

.toggle input {
  display: none;
}

.slider {
  width: 52px;
  height: 28px;
  background: #ccc;
  border-radius: 28px;
  position: relative;
  cursor: pointer;
  transition: background 0.3s;
}

.slider::before {
  content: "";
  position: absolute;
  width: 22px;
  height: 22px;
  background: #fff;
  border-radius: 50%;
  top: 3px;
  left: 3px;
  transition: transform 0.3s;
}

.toggle input:checked + .slider {
  background: #0b5ed7;
}

.toggle input:checked + .slider::before {
  transform: translateX(24px);
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

  <label>Labor</label>
  <select id="labor">
    <option value="200">$200</option>
    <option value="220" selected>$220 (Standard)</option>
    <option value="250">$250</option>
  </select>

  <hr>

  <!-- MOBILE TOGGLES -->
  <div class="toggle">
    <span>Materials Fee ($50)</span>
    <label>
      <input type="checkbox" id="materials">
      <div class="slider"></div>
    </label>
  </div>

  <div class="toggle">
    <span>Travel Fee ($15)</span>
    <label>
      <input type="checkbox" id="travel">
      <div class="slider"></div>
    </label>
  </div>

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
  const labor = Number(document.getElementById("labor").value);

  const materials = document.getElementById("materials").checked ? 50 : 0;
  const travel = document.getElementById("travel").checked ? 15 : 0;

  const extraPartName = document.getElementById("extraPartName").value;
  const extraPartCost = Number(document.getElementById("extraPartCost").value) || 0;

  // MARKUPS
  const glassWithMarkup = glass * 1.25;
  const moldingWithMarkup = molding * 1.25;
  const extraPartWithMarkup = extraPartCost * 1.25;

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
    adas +
    travel;

  const subtotal = taxableParts + nonTaxable;
  const totalWithTax = subtotal + tax;

  // PAYMENT FEE
  const paymentFee = totalWithTax * 0.04;
  const finalTotal = totalWithTax + paymentFee;

  document.getElementById("total").innerHTML =
    "Estimated Total: $" + finalTotal.toFixed(2);

  let extraPartLine = "";
  if (extraPartCost > 0) {
    extraPartLine = `${extraPartName || "Additional Part"} (25% markup): $${extraPartWithMarkup.toFixed(2)}<br>`;
  }

  let materialsLine = materials ? "Materials: $50.00<br>" : "";
  let travelLine = travel ? "Travel Fee (non-taxable): $15.00<br>" : "";

  document.getElementById("breakdown").innerHTML = `
    Glass (25% markup): $${glassWithMarkup.toFixed(2)}<br>
    Molding (25% markup): $${moldingWithMarkup.toFixed(2)}<br>
    Rain Sensor Kit: $${rainSensor.toFixed(2)}<br>
    ${materialsLine}
    ${extraPartLine}
    Labor (non-taxable): $${labor.toFixed(2)}<br>
    ADAS (non-taxable): $${adas.toFixed(2)}<br>
    ${travelLine}
    Tax (8.25% on parts only): $${tax.toFixed(2)}<br>
    Payment Fee (4%): $${paymentFee.toFixed(2)}
  `;
}
</script>
</body>
</html>
