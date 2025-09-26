
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title></title>
<style>
  body {
    font-family: 'Segoe UI', sans-serif;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    margin: 0;
    background: #fdfdfd;
    color: #111;
    text-align: center;
  }
  h1 {
    font-size: 24px;
    margin: 10px 0 20px 0;
  }
  img.logo {
    width: 100px;
    height: auto;
    margin-bottom: 10px;
  }
  input, select, button {
    margin: 5px;
    padding: 8px 12px;
    font-size: 14px;
    border: 1px solid #ccc;
    border-radius: 6px;
    outline: none;
  }
  button {
    background: #007bff;
    color: #fff;
    border: none;
    cursor: pointer;
  }
  button:hover {
    background: #0056b3;
  }
  img#preview {
    margin-top: 20px;
    max-width: 90%;
    border-radius: 8px;
    border: 1px solid #ccc;
  }
  a {
    margin-top: 10px;
    display: inline-block;
    text-decoration: none;
    color: #007bff;
    font-weight: bold;
  }
  a:hover {
    color: #0056b3;
  }
  label {
    font-size: 14px;
  }
</style>
</head>
<body>

<!-- Logo -->
<img src="https://i.ibb.co/N6p4zrzh/Pick-a-pixil.png" alt="Logo" class="logo">

<h1></h1>

<input type="file" id="upload" accept="image/*"><br>

<input type="number" id="width" placeholder="Width (px)">
<input type="number" id="height" placeholder="Height (px)"><br>

<select id="format">
  <option value="image/jpeg">JPG</option>
  <option value="image/png">PNG</option>
  <option value="image/webp">WEBP</option>
</select><br>

<label>Max Size: <input type="number" id="maxSize" placeholder="KB" min="1"></label><br>

<button id="processBtn">Resize & Convert</button>
<a id="downloadLink" style="display:none;">Download Image</a>

<canvas id="canvas" style="display:none;"></canvas>
<img id="preview" src="" alt="Preview">

<script>
const upload = document.getElementById('upload');
const widthInput = document.getElementById('width');
const heightInput = document.getElementById('height');
const formatSelect = document.getElementById('format');
const maxSizeInput = document.getElementById('maxSize');
const processBtn = document.getElementById('processBtn');
const downloadLink = document.getElementById('downloadLink');
const preview = document.getElementById('preview');
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');

let img = new Image();

upload.addEventListener('change', (e) => {
  const file = e.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = function(event) {
    img.src = event.target.result;
  }
  reader.readAsDataURL(file);
});

img.onload = () => {
  preview.src = img.src;
}

processBtn.addEventListener('click', () => {
  let w = parseInt(widthInput.value) || img.width;
  let h = parseInt(heightInput.value) || img.height;
  canvas.width = w;
  canvas.height = h;
  ctx.drawImage(img, 0, 0, w, h);

  const format = formatSelect.value;
  let maxSizeKB = parseInt(maxSizeInput.value) || 0;

  function compress(quality=0.9) {
    canvas.toBlob((blob) => {
      let sizeKB = blob.size / 1024;
      if (maxSizeKB && sizeKB > maxSizeKB && quality > 0.1 && format !== 'image/png') {
        compress(quality - 0.05);
      } else {
        const url = URL.createObjectURL(blob);
        preview.src = url;
        downloadLink.href = url;
        downloadLink.download = 'resized_image.' + format.split('/')[1];
        downloadLink.style.display = 'inline-block';
        downloadLink.textContent = `Download Image (${(blob.size/1024).toFixed(1)} KB)`;
      }
    }, format, quality);
  }

  compress();
});
</script>
<footer style="margin-top: 30px; color: #555; font-size: 14px;">
  Made with ❤ by Armeen
</footer>


</body>
</html>
