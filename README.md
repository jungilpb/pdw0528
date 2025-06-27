<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>골판지 적재 시뮬레이터</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f8f9fa;
      padding: 30px;
    }
    .container {
      max-width: 1000px;
      margin: auto;
      background: #fff;
      padding: 30px;
      border-radius: 8px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    h2 {
      text-align: center;
      color: #343a40;
    }
    label {
      font-weight: bold;
      display: block;
      margin: 15px 0 5px;
    }
    input, select, button {
      width: 100%;
      padding: 10px;
      margin-bottom: 15px;
      border-radius: 5px;
      border: 1px solid #ced4da;
      font-size: 16px;
    }
    button {
      background-color: #007bff;
      color: white;
      border: none;
      cursor: pointer;
    }
    button:hover {
      background-color: #0056b3;
    }
    .result {
      margin-top: 20px;
      padding: 15px;
      background: #e9ecef;
      border-radius: 5px;
    }
  </style>
</head>
<body>
<div class="container">
  <h2>골판지 박스 적재 수량 계산기</h2>

  <label for="boxLength">박스 가로(mm)</label>
  <input type="number" id="boxLength" placeholder="예: 300">

  <label for="boxWidth">박스 세로(mm)</label>
  <input type="number" id="boxWidth" placeholder="예: 200">

  <label for="boxHeight">박스 높이(mm)</label>
  <input type="number" id="boxHeight" placeholder="예: 150">

  <label for="palletWidth">파레트 가로(mm)</label>
  <select id="palletWidth">
    <option value="1100">1100</option>
    <option value="1200">1200</option>
  </select>

  <label for="palletLength">파레트 세로(mm)</label>
  <select id="palletLength">
    <option value="1100">1100</option>
    <option value="1200">1200</option>
  </select>

  <label for="maxHeight">최대 적재 높이(mm)</label>
  <select id="maxHeight">
    <option value="1600">1600</option>
    <option value="1800">1800</option>
    <option value="2000">2000</option>
  </select>

  <label for="flute">골판지 골 종류</label>
  <select id="flute">
    <option value="">-- 선택 --</option>
    <option value="1.35">E골 (1.2~1.5mm)</option>
    <option value="2.75">B골 (2.5~3mm)</option>
    <option value="4.5">A골 (4~5mm)</option>
    <option value="5.75">BB골 (5.5~6mm)</option>
    <option value="7.5">BA골 (7~8mm)</option>
    <option value="4.75">EB골 (4.5~5mm)</option>
  </select>

  <button onclick="calculateStacking()">적재 수량 계산</button>

  <div class="result" id="result"></div>
</div>

<script>
function calculateStacking() {
  const boxLength = parseFloat(document.getElementById('boxLength').value);
  const boxWidth = parseFloat(document.getElementById('boxWidth').value);
  const boxHeight = parseFloat(document.getElementById('boxHeight').value);
  const palletWidth = parseFloat(document.getElementById('palletWidth').value);
  const palletLength = parseFloat(document.getElementById('palletLength').value);
  const maxHeightRaw = parseFloat(document.getElementById('maxHeight').value);
  const thickness = parseFloat(document.getElementById('flute').value);

  if (!boxLength || !boxWidth || !boxHeight || !palletWidth || !palletLength || !maxHeightRaw || !thickness) {
    document.getElementById('result').innerHTML = '<p style="color:red">모든 값을 입력해주세요.</p>';
    return;
  }

  const flatLength = boxLength + boxWidth;
  const flatWidth = boxWidth + boxHeight;

  const cols = Math.floor(palletWidth / flatLength);
  const rows = Math.floor(palletLength / flatWidth);
  const perLayer = cols * rows;

  const stackingHeight = thickness * 2;
  const usableHeight = maxHeightRaw - 150; // 파레트 높이 제외
  const layers = Math.floor(usableHeight / stackingHeight);
  let totalBoxes = perLayer * layers;

  // 여유 공간 계산 후 세워쌓기 추가 (현실적 제한: 높이 조건 포함)
  const remWidth = palletWidth - cols * flatLength;
  const remLength = palletLength - rows * flatWidth;

  const uprightWidth = boxHeight;
  const uprightDepth = boxLength;
  const uprightHeight = boxWidth + boxHeight;

  const uprightCols = Math.floor(remWidth / uprightWidth);
  const uprightRows = Math.floor(palletLength / uprightDepth);
  const uprightLayers = Math.floor(usableHeight / uprightHeight);

  const uprightBoxes = uprightCols * uprightRows * uprightLayers;
  totalBoxes += uprightBoxes;

  document.getElementById('result').innerHTML =
    `<p>펼친 상태 가로: ${flatLength}mm, 세로: ${flatWidth}mm</p>
     <p>파레트 1단 적재 수량 (기본): ${perLayer}개</p>
     <p>세워쌓기 추가 적재 수량: ${uprightBoxes}개</p>
     <p><strong>총 적재 가능 수량: ${totalBoxes}개</strong></p>`;
}
</script>
</body>
</html>
