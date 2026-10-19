<!DOCTYPE html>
<html lang="en" data-theme="neon">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>NovaCalc Secure Vault Pro</title>
  <style>
    :root[data-theme="neon"] {
      --bg1: #05050a;
      --bg2: #0d1124;
      --card: rgba(12, 16, 36, 0.94);
      --border: rgba(0, 255, 200, 0.35);
      --text: #f0fdfa;
      --muted: #64b5a6;
      --accent: #00ffc8;
      --glow: rgba(0, 255, 200, 0.45);
      --btn: rgba(255, 255, 255, 0.06);
      --btn-op: rgba(0, 255, 200, 0.16);
      --eq: linear-gradient(135deg, #00ffc8, #00b4d8);
      --eq-text: #04131a;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      user-select: none;
      -webkit-tap-highlight-color: transparent;
      touch-action: manipulation;
    }

    body {
      min-height: 100vh;
      font-family: system-ui, -apple-system, sans-serif;
      background: radial-gradient(circle at top, var(--bg2), var(--bg1));
      color: var(--text);
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 12px;
      position: relative;
    }

    .calc-app {
      width: 100%;
      max-width: 420px;
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: 26px;
      padding: 18px;
      box-shadow: 0 20px 50px rgba(0, 0, 0, 0.8), 0 0 25px var(--glow);
      backdrop-filter: blur(20px);
    }

    /* Secret Gallery / Vault Panel */
    .vault-panel {
      position: fixed;
      inset: 0;
      background: #040508;
      z-index: 99;
      display: none;
      flex-direction: column;
      padding: 20px;
      justify-content: space-between;
      animation: fadeIn 0.25s ease;
    }

    .vault-panel.show {
      display: flex;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: scale(0.97); }
      to { opacity: 1; transform: scale(1); }
    }

    .vault-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 1px solid rgba(255,255,255,0.1);
      padding-bottom: 12px;
    }

    .vault-title {
      font-size: 1.1rem;
      font-weight: 800;
      color: #00ffc8;
      letter-spacing: 1px;
    }

    .media-grid {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 12px;
      max-height: 60vh;
      overflow-y: auto;
      margin: 10px 0;
      padding: 4px;
    }

    .media-card {
      background: rgba(255, 255, 255, 0.04);
      border: 1px solid rgba(255, 255, 255, 0.1);
      border-radius: 14px;
      padding: 14px;
      text-align: center;
      cursor: pointer;
      transition: 0.2s;
    }

    .media-card:hover {
      background: rgba(0, 255, 200, 0.1);
      border-color: var(--accent);
    }

    .media-card h3 {
      font-size: 0.95rem;
      margin-bottom: 4px;
    }

    .media-card span {
      font-size: 0.72rem;
      color: var(--muted);
    }

    .preview-container {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 8px;
      margin-top: 10px;
      max-height: 180px;
      overflow-y: auto;
    }

    .preview-item {
      width: 100%;
      height: 80px;
      border-radius: 8px;
      object-fit: cover;
      border: 1px solid rgba(255,255,255,0.2);
    }

    /* Calculator UI */
    .top-bar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 12px;
      gap: 8px;
    }

    .brand {
      font-weight: 800;
      font-size: 1rem;
      letter-spacing: 1px;
      color: var(--accent);
      text-shadow: 0 0 10px var(--glow);
    }

    .status-pill {
      font-size: 0.7rem;
      font-weight: 700;
      padding: 4px 8px;
      border-radius: 20px;
      background: rgba(0, 255, 200, 0.15);
      border: 1px solid var(--accent);
      color: var(--accent);
      cursor: pointer;
    }

    .screen {
      background: rgba(0, 0, 0, 0.5);
      border: 1px solid var(--border);
      border-radius: 18px;
      padding: 16px;
      margin-bottom: 14px;
      text-align: right;
      min-height: 105px;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      overflow: hidden;
    }

    .expr-text {
      font-size: 0.95rem;
      color: var(--muted);
      min-height: 22px;
      word-break: break-all;
      font-family: monospace;
    }

    .res-text {
      font-size: 2.3rem;
      font-weight: 800;
      color: var(--text);
      text-shadow: 0 0 15px var(--glow);
      word-break: break-all;
      font-family: monospace;
    }

    .num-pad {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
    }

    .btn {
      padding: 16px 6px;
      border-radius: 15px;
      border: 1px solid var(--border);
      background: var(--btn);
      color: var(--text);
      font-size: 1.2rem;
      font-weight: 700;
      cursor: pointer;
      transition: transform 0.08s, background 0.15s;
    }

    .btn:active {
      transform: scale(0.92);
      background: var(--btn-op);
    }

    .btn.op { background: var(--btn-op); color: var(--accent); }
    .btn.danger { background: rgba(244, 63, 94, 0.22); color: #ff4d6d; border-color: rgba(244, 63, 94, 0.45); }
    .btn.eq { background: var(--eq); color: var(--eq-text); border: none; box-shadow: 0 4px 18px var(--glow); }
  </style>
</head>
<body>

  <!-- Calculator Screen -->
  <div class="calc-app" id="calcApp">
    <div class="top-bar">
      <div class="brand">⚡ NOVACALC PRO</div>
      <div class="status-pill" id="musicToggle">🎵 Music: OFF</div>
    </div>

    <div class="screen">
      <div class="expr-text" id="exprScreen">0</div>
      <div class="res-text" id="resScreen">0</div>
    </div>

    <div class="num-pad">
      <button class="btn danger" data-act="clear">AC</button>
      <button class="btn op" data-act="del">⌫</button>
      <button class="btn op" data-op="%">%</button>
      <button class="btn op" data-op="/">÷</button>

      <button class="btn" data-num="7">7</button>
      <button class="btn" data-num="8">8</button>
      <button class="btn" data-num="9">9</button>
      <button class="btn op" data-op="*">×</button>

      <button class="btn" data-num="4">4</button>
      <button class="btn" data-num="5">5</button>
      <button class="btn" data-num="6">6</button>
      <button class="btn op" data-op="-">−</button>

      <button class="btn" data-num="1">1</button>
      <button class="btn" data-num="2">2</button>
      <button class="btn" data-num="3">3</button>
      <button class="btn op" data-op="+">+</button>

      <button class="btn" data-act="sign">±</button>
      <button class="btn" data-num="0">0</button>
      <button class="btn" data-num=".">.</button>
      <button class="btn eq" data-act="eq">=</button>
    </div>
  </div>

  <!-- Secret Vault Panel (Opens on entering PIN: 7896 =) -->
  <div class="vault-panel" id="vaultPanel">
    <div class="vault-header">
      <div class="vault-title">🔒 SECURE MEDIA VAULT</div>
      <button class="btn" style="padding: 6px 14px; font-size: 0.78rem;" id="lockVaultBtn">Lock & Exit</button>
    </div>

    <div>
      <p style="color: var(--muted); font-size: 0.8rem; margin-bottom: 10px; text-align: center;">
        Apne phone ki gallery se photos aur videos yahan securely hide karein.
      </p>

      <div class="media-grid">
        <!-- Hidden file inputs to trigger phone gallery -->
        <input type="file" id="photoInput" accept="image/*" multiple style="display:none;" />
        <input type="file" id="videoInput" accept="video/*" multiple style="display:none;" />

        <div class="media-card" onclick="document.getElementById('photoInput').click()">
          <h3>📷 Add Photos</h3>
          <span id="photoCount">0 Hidden</span>
        </div>
        <div class="media-card" onclick="document.getElementById('videoInput').click()">
          <h3>🎬 Add Videos</h3>
          <span id="videoCount">0 Hidden</span>
        </div>
      </div>

      <div style="font-size: 0.8rem; font-weight: 700; color: var(--accent); margin-top: 6px;">Saved Media Preview:</div>
      <div class="preview-container" id="previewContainer">
        <div style="color: #555; font-size: 0.75rem; grid-column: span 3; text-align: center; padding-top: 20px;">
          No media added yet. Tap above to import from gallery!
        </div>
      </div>
    </div>

    <div style="text-align: center; font-size: 0.72rem; color: #555;">
      Secret Trigger: Type <b>7896</b> and press <b>=</b>
    </div>
  </div>

  <script>
    (function () {
      var exprScreen = document.getElementById("exprScreen");
      var resScreen = document.getElementById("resScreen");
      var vaultPanel = document.getElementById("vaultPanel");
      var lockVaultBtn = document.getElementById("lockVaultBtn");
      var photoInput = document.getElementById("photoInput");
      var videoInput = document.getElementById("videoInput");
      var previewContainer = document.getElementById("previewContainer");
      var photoCount = document.getElementById("photoCount");
      var videoCount = document.getElementById("videoCount");
      var musicToggle = document.getElementById("musicToggle");

      var current = "0";
      var tokens = [];
      var resetNext = false;
      
      // 🔑 SECRET PIN SET TO 7896
      var SECRET_PIN = "7896";
      var savedMediaCount = 0;
      var musicPlaying = false;
      var audioCtx = null;
      var musicInterval = null;

      // Ambient Chill Sound Generator
      function toggleMusic() {
        musicPlaying = !musicPlaying;
        musicToggle.innerText = musicPlaying ? "🎵 Music: ON" : "🎵 Music: OFF";
        
        try {
          var AudioCtxClass = window.AudioContext || window.webkitAudioContext;
          if (!AudioCtxClass) return;
          if (!audioCtx) audioCtx = new AudioCtxClass();
          if (audioCtx.state === "suspended") audioCtx.resume();

          if (musicPlaying) {
            var notes = [261.63, 329.63, 392.00, 523.25]; // Chill chord notes
            musicInterval = setInterval(function() {
              if (!musicPlaying) return;
              var osc = audioCtx.createOscillator();
              var gain = audioCtx.createGain();
              osc.type = "sine";
              osc.frequency.setValueAtTime(notes[Math.floor(Math.random() * notes.length)], audioCtx.currentTime);
              gain.gain.setValueAtTime(0.015, audioCtx.currentTime);
              gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + 1.5);
              osc.connect(gain);
              gain.connect(audioCtx.destination);
              osc.start();
              osc.stop(audioCtx.currentTime + 1.6);
            }, 1800);
          } else {
            if (musicInterval) clearInterval(musicInterval);
          }
        } catch(e) {}
      }

      musicToggle.addEventListener("click", toggleMusic);

      function render() {
        var displayTokens = [];
        for (var i = 0; i < tokens.length; i++) {
          var t = tokens[i];
          if (t === "*") displayTokens.push("×");
          else if (t === "/") displayTokens.push("÷");
          else if (t === "-") displayTokens.push("−");
          else displayTokens.push(t);
        }
        exprScreen.innerText = displayTokens.length ? displayTokens.join(" ") : "0";
        resScreen.innerText = current;
      }

      function solveMath(arrInput) {
        var arr = [];
        for (var i = 0; i < arrInput.length; i++) {
          var item = arrInput[i];
          if (item === "+" || item === "-" || item === "*" || item === "/" || item === "%") {
            arr.push(item);
          } else {
            arr.push(parseFloat(item));
          }
        }

        for (var j = 1; j < arr.length; j += 2) {
          var op = arr[j];
          if (op === "*" || op === "/" || op === "%") {
            var a = arr[j - 1];
            var b = arr[j + 1];
            if ((op === "/" || op === "%") && b === 0) return NaN;
            var r = op === "*" ? a * b : op === "/" ? a / b : a % b;
            arr.splice(j - 1, 3, r);
            j -= 2;
          }
        }

        var sum = arr[0] || 0;
        for (var k = 1; k < arr.length; k += 2) {
          var nextOp = arr[k];
          var nextNum = arr[k + 1] || 0;
          if (nextOp === "+") sum += nextNum;
          if (nextOp === "-") sum -= nextNum;
        }
        return parseFloat(Number(sum).toFixed(10));
      }

      // Number Buttons
      var numBtns = document.querySelectorAll("[data-num]");
      for (var i = 0; i < numBtns.length; i++) {
        numBtns[i].addEventListener("click", function () {
          var val = this.getAttribute("data-num");
          if (resetNext) {
            tokens = [];
            current = val === "." ? "0." : val;
            resetNext = false;
            render();
            return;
          }
          if (val === "." && current.indexOf(".") !== -1) return;
          current = current === "0" && val !== "." ? val : current + val;
          render();
        });
      }

      // Operator Buttons
      var opBtns = document.querySelectorAll("[data-op]");
      for (var j = 0; j < opBtns.length; j++) {
        opBtns[j].addEventListener("click", function () {
          var op = this.getAttribute("data-op");
          resetNext = false;
          tokens.push(current);
          tokens.push(op);
          current = "0";
          render();
        });
      }

      // Action Buttons
      var actBtns = document.querySelectorAll("[data-act]");
      for (var k = 0; k < actBtns.length; k++) {
        actBtns[k].addEventListener("click", function () {
          var act = this.getAttribute("data-act");

          if (act === "clear") {
            current = "0";
            tokens = [];
            resetNext = false;
            render();
          } else if (act === "del") {
            if (resetNext) {
              current = "0";
              tokens = [];
              resetNext = false;
            } else {
              current = current.length > 1 ? current.slice(0, -1) : "0";
            }
            render();
          } else if (act === "sign") {
            if (current !== "0") {
              current = current.charAt(0) === "-" ? current.slice(1) : "-" + current;
              render();
            }
          } else if (act === "eq") {
            // 🕵️ SECRET PIN TRIGGER CHECK (7896 =)
            if (tokens.length === 0 && current === SECRET_PIN) {
              vaultPanel.classList.add("show");
              current = "0";
              render();
              return;
            }

            var full = tokens.concat([current]);
            var ans = solveMath(full);
            var formulaText = full.join(" ");
            
            if (isNaN(ans) || !isFinite(ans)) {
              exprScreen.innerText = formulaText + " =";
              resScreen.innerText = "Error";
              tokens = [];
              current = "0";
              return;
            }

            exprScreen.innerText = formulaText + " =";
            current = String(ans);
            resScreen.innerText = current;
            tokens = [];
            resetNext = true;
          }
        });
      }

      // Handle Gallery Photo/Video Selection
      photoInput.addEventListener("change", function(e) {
        handleFiles(e.target.files, "photo");
      });
      videoInput.addEventListener("change", function(e) {
        handleFiles(e.target.files, "video");
      });

      function handleFiles(files, type) {
        if (!files || files.length === 0) return;
        if (savedMediaCount === 0) {
          previewContainer.innerHTML = "";
        }
        
        for (var idx = 0; idx < files.length; idx++) {
          var file = files[idx];
          var reader = new FileReader();
          reader.onload = (function(f) {
            return function(e) {
              var item = document.createElement(type === "photo" ? "img" : "video");
              item.src = e.target.result;
              item.className = "preview-item";
              if (type === "video") item.controls = true;
              previewContainer.appendChild(item);
            };
          })(file);
          reader.readAsDataURL(file);
          savedMediaCount++;
        }
        photoCount.innerText = savedMediaCount + " Hidden";
      }

      lockVaultBtn.addEventListener("click", function () {
        vaultPanel.classList.remove("show");
        current = "0";
        tokens = [];
        render();
      });
    })();
  </script>
</body>
</html>
