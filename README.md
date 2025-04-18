<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Happy Birthday Sonia</title>
  <style>
    body {
      margin: 0;
      padding: 0;
      overflow: hidden;
      background-color: #000;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    canvas {
      display: block;
      position: absolute;
      top: 0;
      left: 0;
    }

    .message {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      color: #fff;
      font-size: 3em;
      text-align: center;
      text-shadow: 0 0 10px #ff00ff, 0 0 20px #ff00ff, 0 0 40px #ff00ff;
      animation: pulse 2s infinite;
      z-index: 1;
    }

    @keyframes pulse {
      0% { transform: translate(-50%, -50%) scale(1); }
      50% { transform: translate(-50%, -50%) scale(1.1); }
      100% { transform: translate(-50%, -50%) scale(1); }
    }
  </style>
</head>
<body>
  <div class="message">Feliz Cumoleaños<br>mi niña</div>
  <canvas id="canvas"></canvas>

  <audio autoplay controls loop>
    <source src="1.mp3" type="audio/mpeg">
  </audio>

  <script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    let t1 = 0;
    let t2 = Math.PI;
    const trail = [];

    // Fireworks particles
    const fireworks = [];

    class Particle {
      constructor(x, y, color, vx, vy) {
        this.x = x;
        this.y = y;
        this.color = color;
        this.vx = vx;
        this.vy = vy;
        this.alpha = 1;
      }

      update() {
        this.x += this.vx;
        this.y += this.vy;
        this.vy += 0.05;
        this.alpha -= 0.01;
      }

      draw() {
        ctx.globalAlpha = this.alpha;
        ctx.beginPath();
        ctx.arc(this.x, this.y, 2, 0, Math.PI * 2);
        ctx.fillStyle = this.color;
        ctx.fill();
        ctx.globalAlpha = 1;
      }
    }

    function launchFirework() {
      const x = Math.random() * canvas.width;
      const y = Math.random() * canvas.height / 2;
      const colors = ['#ff0040', '#00ffea', '#ffff00', '#ff00ff', '#00ff00'];
      const color = colors[Math.floor(Math.random() * colors.length)];

      for (let i = 0; i < 50; i++) {
        const angle = Math.random() * Math.PI * 2;
        const speed = Math.random() * 4 + 1;
        const vx = Math.cos(angle) * speed;
        const vy = Math.sin(angle) * speed;
        fireworks.push(new Particle(x, y, color, vx, vy));
      }
    }

    function drawFireworks() {
      for (let i = fireworks.length - 1; i >= 0; i--) {
        const p = fireworks[i];
        p.update();
        p.draw();
        if (p.alpha <= 0) fireworks.splice(i, 1);
      }
    }

    function heartPath(t) {
      const scale = 10;
      const x = scale * 16 * Math.pow(Math.sin(t), 3);
      const y = -scale * (13 * Math.cos(t) - 5 * Math.cos(2 * t) - 2 * Math.cos(3 * t) - Math.cos(4 * t));
      return { x, y };
    }

    function drawHeartOutline() {
      ctx.beginPath();
      for (let t = 0; t < Math.PI * 2; t += 0.01) {
        const { x, y } = heartPath(t);
        const cx = canvas.width / 2 + x;
        const cy = canvas.height / 2 + y;
        if (t === 0) ctx.moveTo(cx, cy);
        else ctx.lineTo(cx, cy);
      }
      ctx.strokeStyle = 'rgba(255, 0, 255, 0.2)';
      ctx.lineWidth = 2;
      ctx.shadowColor = 'magenta';
      ctx.shadowBlur = 10;
      ctx.stroke();
    }

    function drawTrail() {
      for (let i = 0; i < trail.length; i++) {
        const { x, y, color, size } = trail[i];
        ctx.beginPath();
        ctx.arc(canvas.width / 2 + x, canvas.height / 2 + y, size, 0, Math.PI * 2);
        ctx.fillStyle = color;
        ctx.shadowColor = color;
        ctx.shadowBlur = 15;
        ctx.fill();
      }
    }

    function draw() {
      ctx.fillStyle = 'rgba(0, 0, 0, 0.2)';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      drawHeartOutline();
      drawTrail();
      drawFireworks();

      [[t1, 'cyan'], [t2, 'hotpink']].forEach(([t, color]) => {
        const { x, y } = heartPath(t);
        trail.push({ x, y, color, size: 5 });
      });

      if (trail.length > 500) trail.splice(0, 5);

      t1 += 0.02;
      t2 += 0.02;
      if (t1 > Math.PI * 2) t1 = 0;
      if (t2 > Math.PI * 2) t2 = 0;

      requestAnimationFrame(draw);
    }

    setInterval(launchFirework, 800);
    draw();
  </script>
</body>
</html>

