# Handwritten-Digit-Recognition-
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Digit Recognizer</title>
  <style>body {
  text-align: center;
  font-family: Arial, sans-serif;
  background: #f9f9f9;
  padding: 20px;
}

canvas {
  border: 2px solid #000;
  background: white;
  margin-bottom: 10px;
  cursor: crosshair;
}

button {
  margin: 5px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}
</style>
</head>
<script >let canvas = document.getElementById("canvas");
let ctx = canvas.getContext("2d");
let isDrawing = false;

canvas.addEventListener("mousedown", () => isDrawing = true);
canvas.addEventListener("mouseup", () => isDrawing = false);
canvas.addEventListener("mousemove", draw);

function draw(e) {
  if (!isDrawing) return;
  ctx.lineWidth = 15;
  ctx.lineCap = "round";
  ctx.strokeStyle = "#000";
  ctx.lineTo(e.offsetX, e.offsetY);
  ctx.stroke();
  ctx.beginPath();
  ctx.moveTo(e.offsetX, e.offsetY);
}

function clearCanvas() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.beginPath();
  document.getElementById("result").innerText = "Prediction: ?";
}

async function predict() {
  let tensor = preprocessCanvas(canvas);
  const model = await tf.loadLayersModel("model/model.json");
  let prediction = model.predict(tensor).argMax(1).dataSync()[0];
  document.getElementById("result").innerText = `Prediction: ${prediction}`;
}

function preprocessCanvas(canvas) {
  let temp = document.createElement("canvas");
  temp.width = 28;
  temp.height = 28;
  let tempCtx = temp.getContext("2d");
  tempCtx.drawImage(canvas, 0, 0, 28, 28);
  let imgData = tempCtx.getImageData(0, 0, 28, 28);
  let gray = [];

  for (let i = 0; i < imgData.data.length; i += 4) {
    let avg = imgData.data[i] / 255; // Grayscale and normalize
    gray.push(avg);
  }

  return tf.tensor([gray], [1, 28, 28, 1]);
}</script>
<body>
  <h1>Draw a Digit (0-9)</h1>
  <canvas id="canvas" width="280" height="280"></canvas>
  <div>
    <button onclick="clearCanvas()">Clear</button>
    <button onclick="predict()">Predict</button>
  </div>
  <h2 id="result">Prediction: ?</h2>

  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@4.2.0"></script>
  <script src="script.js"></script>
</body>
</html>
