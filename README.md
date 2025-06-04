<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <title>Vòng quay may mắn</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      text-align: center;
      background: linear-gradient(135deg, #3a0ca3, #9d4edd);
      color: white;
      margin: 0;
      padding: 0;
    }
    h1 {
      margin-top: 30px;
      font-size: 36px;
      color: #f3c4fb;
      text-shadow: 0 0 10px #c77dff;
    }
    form {
      margin-top: 20px;
    }
    input[type="text"], input[type="email"] {
      padding: 10px;
      font-size: 16px;
      border-radius: 8px;
      border: none;
      margin: 10px;
      width: 250px;
      outline: none;
    }
    #wheel {
      width: 300px;
      height: 300px;
      border-radius: 50%;
      border: 12px solid #f3c4fb;
      position: relative;
      margin: 40px auto;
      box-shadow: 0 0 30px rgba(255, 255, 255, 0.3);
      background-color: #240046;
      overflow: visible;
      cursor: pointer;
    }
    #spinButton {
      padding: 15px 30px;
      font-size: 18px;
      background: linear-gradient(135deg, #ff6ec4, #7873f5);
      color: white;
      border: none;
      border-radius: 12px;
      cursor: pointer;
      box-shadow: 0 4px 15px rgba(255, 255, 255, 0.2);
      transition: background 0.3s;
    }
    #spinButton:hover {
      background: linear-gradient(135deg, #ff8fe6, #a499f5);
    }
    #result {
      margin-top: 20px;
      font-size: 24px;
      color: #ffc6ff;
      text-shadow: 0 0 5px #3c096c;
      white-space: pre-line;
    }
    #arrow {
      width: 0;
      height: 0;
      border-left: 20px solid transparent;
      border-right: 20px solid transparent;
      border-bottom: 30px solid #ff70a6;
      margin: 0 auto;
      margin-top: -10px;
      filter: drop-shadow(0 0 3px #ff70a6);
    }
    @media screen and (max-width: 600px) {
      #wheel {
        width: 250px;
        height: 250px;
      }
      input[type="text"], input[type="email"] {
        width: 80%;
      }
    }
  </style>
</head>
<body>
  <h1>🏱 Vòng quay may mắn 🏱</h1>
  <form id="userForm">
    <input type="text" id="name" placeholder="Nhập tên của bạn" required />
    <input type="email" id="email" placeholder="Nhập email của bạn" required /><br />
  </form>
  <div id="arrow"></div>
  <canvas id="wheel" width="300" height="300"></canvas>
  <button id="spinButton">Quay ngay</button>
  <div id="result"></div>

  <audio id="spinSound" src="https://www.soundjay.com/button/sounds/button-3.mp3" preload="auto"></audio>
  <audio id="winSound" src="https://www.soundjay.com/human/sounds/applause-8.mp3" preload="auto"></audio>

  <script>
    const rewards = [
      { prize: "Voucher 10%", weight: 50 },
      { prize: "Voucher 15%", weight: 30 },
      { prize: "Voucher 20%", weight: 10 },
      { prize: "Voucher 30%", weight: 5 },
      { prize: "Chúc bạn may mắn lần sau", weight: 5 }
    ];

    const codes = {
      "Voucher 10%": ["10-AP97ZY", "10-WUQZNB", "10-M355W9", "10-4REIUE", "10-RFDMKC", "10-8ST825", "10-ZCUO7B", "10-7ABQQ0", "10-5TFXLP", "10-VKF9OP", "10-3EI58W", "10-I1XMNM", "10-7AJWD9", "10-7N8HZY", "10-WJKEPD", "10-OPL115", "10-NFPMT7", "10-IJ2R7C", "10-86WSVL", "10-V9SF0T", "10-VVTCGZ", "10-BRIIZ4", "10-7GP622", "10-JMDRD8", "10-TZUGBM", "10-TPQGG1", "10-KQMKGD", "10-TALXML", "10-ULXZYO", "10-YLN9HV", "10-GUCV5P", "10-ML9SOC", "10-N4XYV2", "10-P1KE78", "10-CF6CSY", "10-0E8IAB", "10-6PTKA7", "10-RCP9WS", "10-FZF0OZ", "10-Z6V5AS", "10-OZHMDK", "10-ZUPP2M", "10-I1ICRM", "10-A2UXSR", "10-E7VROD", "10-FH29LX", "10-GWEGO4", "10-I26T3R", "10-K11CBE", "10-SR3PB4"],
      "Voucher 15%": ["15-HSHDYA", "15-LQVOED", "15-0KXTYB", "15-JUY3Y1", "15-10SJPJ", "15-ZACG6U", "15-D4L0Q3", "15-R8VKSR", "15-9R2NZ4", "15-QNYEG8", "15-B35N4G", "15-4FNN7D", "15-GBPZKZ", "15-7FBVVQ", "15-3VWB25", "15-STYWTA", "15-1WJONP", "15-NG4BID", "15-FUH66G", "15-8BBIVK", "15-TLS0GL", "15-65XK6O", "15-VNSMS5", "15-PEV3NU", "15-04I86P", "15-2GKLA2", "15-PN55J4", "15-NMVJ7E", "15-ZL5A13", "15-Z2ANLT"],
      "Voucher 20%": ["20-7VOR54", "20-2AD5FT", "20-MVS7NS", "20-ZKJ35Z", "20-ZN3F1Q", "20-K6QHZE", "20-EK8QUV", "20-7W2VYR", "20-CF2SDX", "20-3UNJS4"],
      "Voucher 30%": ["30-BNHAW6", "30-CWHWJ0", "30-L9XT1W", "30-41BB9C", "30-UFQI4J"],
      "Chúc bạn may mắn lần sau": []
    };

    const usedEmails = new Set();
    const savedResults = [];

    const canvas = document.getElementById("wheel");
    const ctx = canvas.getContext("2d");
    const centerX = canvas.width / 2;
    const centerY = canvas.height / 2;
    const radius = 140;

    const segmentColors = [
      "#9d4edd",
      "#c77dff",
      "#7b2ff7",
      "#a463f2",
      "#6a0dad"
    ];

    function drawWheel() {
      const totalSegments = rewards.length;
      const arcSize = (2 * Math.PI) / totalSegments;

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      for (let i = 0; i < totalSegments; i++) {
        // Màu nền mỗi nhánh
        ctx.beginPath();
        ctx.moveTo(centerX, centerY);
        ctx.fillStyle = segmentColors[i % segmentColors.length];
        ctx.arc(centerX, centerY, radius, i * arcSize, (i + 1) * arcSize);
        ctx.lineTo(centerX, centerY);
        ctx.fill();

        // Viền phân đoạn
        ctx.strokeStyle = "#f3c4fb";
        ctx.lineWidth = 2;
        ctx.stroke();

        // Text phần thưởng
        ctx.save();
        ctx.translate(centerX, centerY);
        ctx.rotate(i * arcSize + arcSize / 2);
        ctx.textAlign = "right";
        ctx.fillStyle = "#f3c4fb";
        ctx.font = "bold 16px 'Segoe UI', sans-serif";
        ctx.fillText(rewards[i].prize, radius - 10, 10);
        ctx.restore();
      }
    }

    drawWheel();

    function weightedRandom(rewards) {
      const total = rewards.reduce((sum, r) => sum + r.weight, 0);
      let rand = Math.random() * total;
      for (const reward of rewards) {
        if (rand < reward.weight) return reward.prize;
        rand -= reward.weight;
      }
    }

    let isSpinning = false;
    let currentRotation = 0;
    const spinSound = document.getElementById("spinSound");
    const winSound = document.getElementById("winSound");
    const resultDiv = document.getElementById("result");

    document.getElementById("spinButton").addEventListener("click", () => {
      if (isSpinning) return;

      const name = document.getElementById("name").value.trim();
      const email = document.getElementById("email").value.trim().toLowerCase();

      if (!name || !email) {
        resultDiv.textContent = "Vui lòng nhập tên và email.";
        return;
      }

      if (usedEmails.has(email)) {
        resultDiv.textContent = "Bạn chỉ được quay 1 lần.";
        return;
      }

      isSpinning = true;
      spinSound.play();

      // Chọn phần thưởng dựa trên trọng số
      const result = weightedRandom(rewards);

      // Tính góc quay sao cho phần thưởng nằm ở vị trí mũi tên
      const totalSegments = rewards.length;
      const arcSize = 360 / totalSegments;
      const prizeIndex = rewards.findIndex(r => r.prize === result);

      // Tạo góc quay ngẫu nhiên trong phần thưởng đó (để ko quá "lặp lại")
      const randomAngleInSegment = Math.random() * arcSize;

      // Tính góc quay cần thiết để mũi tên chỉ đúng phần thưởng (360 - (prizeIndex*arcSize + randomAngleInSegment))
      // + quay thêm 5 vòng 360*5 = 1800 độ cho cảm giác quay dài
      const finalRotation = 360 * 5 + (360 - (prizeIndex * arcSize + randomAngleInSegment));

      let startRotation = currentRotation % 360;
      let rotateAmount = finalRotation - startRotation;

      // Animation xoay bánh xe trong 5s
      let startTime = null;

      function animate(timestamp) {
        if (!startTime) startTime = timestamp;
        const elapsed = timestamp - startTime;
        const duration = 5000; // 5 giây

        if (elapsed < duration) {
          const easeOut = 1 - Math.pow(1 - elapsed / duration, 3);
          currentRotation = startRotation + rotateAmount * easeOut;
          canvas.style.transform = `rotate(${currentRotation}deg)`;
          requestAnimationFrame(animate);
        } else {
          currentRotation = startRotation + rotateAmount;
          canvas.style.transform = `rotate(${currentRotation}deg)`;
          isSpinning = false;

          // Xử lý kết quả
          let code = "";
          if (codes[result] && codes[result].length > 0) {
            code = codes[result].pop();
          }

          let message = `✨ ${name}, bạn trúng: ${result}`;
          if (code) message += `\nMã của bạn: ${code}`;

          resultDiv.textContent = message;
          winSound.play();

          usedEmails.add(email);
          savedResults.push({ name, email, result, code });
          console.log(savedResults);

          // TODO: Gửi email tự động (bạn có thể cung cấp EmailJS info để mình tích hợp)
        }
      }

      requestAnimationFrame(animate);
    });
  </script>
</body>
</html>
