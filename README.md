<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Portfolio | ศศิธร เซ้งรักษา (bossu29)</title>
    
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800;900&family=Prompt:wght@300;400;500;600;700&display=swap" rel="stylesheet">

    <!-- Three.js & GSAP -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>

    <style>
        :root {
            --bg-color: #080612;
            --primary-cyan: #87e8cb;
            --primary-purple: #aca9de;
            --accent-gold: #ffd700;
            --accent-red: #ff4757;
            --dark-purple: #120e24;
            --text-color: #f0f4f8;
            --glass-bg: rgba(15, 12, 28, 0.78);
            --border-glow: rgba(135, 232, 203, 0.5);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Prompt', sans-serif;
            user-select: none;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            overflow-x: hidden;
            min-height: 100vh;
        }

        #webgl-bg {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            z-index: 1;
        }

        /* Layout Grid System: ขยับการ์ดไปซ้าย */
        .page-wrapper {
            position: relative;
            z-index: 2;
            width: 100%;
            min-height: 100vh;
            display: flex;
            align-items: center;
            padding: 2.5rem 5%;
            pointer-events: none;
            perspective: 1000px;
        }

        .dnd-card {
            pointer-events: auto;
            width: 100%;
            max-width: 620px; /* จำกัดความกว้างเพื่อไม่ให้บังเต๋าฝั่งขวา */
            background: var(--glass-bg);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 2px solid var(--primary-cyan);
            border-radius: 20px;
            padding: 2.5rem;
            box-shadow: 0 0 40px rgba(135, 232, 203, 0.2),
                        inset 0 0 30px rgba(172, 169, 222, 0.15);
            position: relative;
            transition: transform 0.1s ease-out, box-shadow 0.3s ease;
            transform-style: preserve-3d;
        }

        .dnd-card:hover {
            box-shadow: 0 0 60px rgba(135, 232, 203, 0.4),
                        inset 0 0 40px rgba(172, 169, 222, 0.25);
        }

        .dnd-card::before {
            content: '✦ D&D LEGENDARY SHEET ✦';
            position: absolute;
            top: -14px;
            left: 50%;
            transform: translateX(-50%);
            background: var(--dark-purple);
            color: var(--primary-cyan);
            border: 1px solid var(--primary-cyan);
            padding: 4px 20px;
            border-radius: 20px;
            font-size: 0.8rem;
            letter-spacing: 3px;
            font-family: 'Cinzel', serif;
            font-weight: 800;
            box-shadow: 0 0 15px var(--primary-cyan);
        }

        /* Navigation Bar สำหรับสลับหน้าต่าง */
        .nav-menu {
            display: flex;
            gap: 10px;
            margin-bottom: 1.5rem;
            border-bottom: 1px solid var(--border-glow);
            padding-bottom: 0.8rem;
        }

        .nav-btn {
            background: transparent;
            border: 1px solid transparent;
            color: var(--primary-purple);
            padding: 6px 16px;
            border-radius: 12px;
            font-size: 0.95rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s ease;
            font-family: 'Cinzel', 'Prompt', serif;
        }

        .nav-btn:hover {
            color: var(--primary-cyan);
            border-color: rgba(135, 232, 203, 0.3);
            background: rgba(135, 232, 203, 0.05);
        }

        .nav-btn.active {
            background: var(--primary-cyan);
            color: var(--bg-color);
            border-color: var(--primary-cyan);
            box-shadow: 0 0 15px var(--primary-cyan);
            font-weight: 600;
        }

        /* Content Sections */
        .tab-content {
            display: none;
            animation: fadeIn 0.5s ease-out forwards;
        }

        .tab-content.active {
            display: block;
        }

        .badge-container {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
            margin-bottom: 1rem;
        }

        .class-badge {
            background: linear-gradient(135deg, var(--dark-purple), #2a1f4d);
            color: var(--primary-cyan);
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 0.8rem;
            letter-spacing: 1px;
            border: 1px solid rgba(135, 232, 203, 0.4);
        }

        .brand-title {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 2.2rem;
            font-weight: 900;
            letter-spacing: 3px;
            background: linear-gradient(135deg, #ffffff 10%, var(--primary-cyan) 50%, var(--primary-purple) 90%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            filter: drop-shadow(0 0 12px rgba(135, 232, 203, 0.6));
            margin-bottom: 0.2rem;
        }

        .divider-line {
            border: 0;
            height: 2px;
            background: linear-gradient(90deg, var(--primary-cyan), var(--primary-purple), transparent);
            margin: 0.8rem 0 1.2rem 0;
        }

        h1 {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 2.4rem;
            font-weight: 800;
            color: #ffffff;
            text-shadow: 0 0 15px rgba(135, 232, 203, 0.5);
            margin-bottom: 0.2rem;
        }

        .subtitle {
            font-size: 1.05rem;
            color: var(--primary-purple);
            margin-bottom: 1.5rem;
            font-weight: 300;
        }

        .info-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
            gap: 1.2rem;
            margin-bottom: 1.5rem;
        }

        .info-box {
            background: rgba(18, 14, 36, 0.6);
            border: 1px solid rgba(172, 169, 222, 0.25);
            border-left: 4px solid var(--primary-cyan);
            padding: 1rem;
            border-radius: 6px 14px 14px 6px;
            transition: all 0.3s ease;
        }

        .info-box:hover {
            transform: translateY(-3px);
            border-color: var(--primary-cyan);
        }

        .info-box h3 {
            font-size: 1rem;
            color: var(--primary-cyan);
            margin-bottom: 0.4rem;
            font-family: 'Cinzel', 'Prompt', serif;
        }

        .info-box p {
            font-size: 0.9rem;
            color: #e0ddf5;
            line-height: 1.5;
        }

        /* RPG Stats Progress Bar */
        .stats-section {
            margin-bottom: 1.5rem;
        }

        .stat-bar-container {
            margin-bottom: 0.7rem;
        }

        .stat-header {
            display: flex;
            justify-content: space-between;
            font-size: 0.85rem;
            color: var(--primary-purple);
            margin-bottom: 0.2rem;
        }

        .stat-bar-bg {
            width: 100%;
            height: 7px;
            background: rgba(255, 255, 255, 0.08);
            border-radius: 4px;
            overflow: hidden;
            border: 1px solid rgba(135, 232, 203, 0.2);
        }

        .stat-bar-fill {
            height: 100%;
            background: linear-gradient(90deg, var(--primary-purple), var(--primary-cyan));
            border-radius: 4px;
            box-shadow: 0 0 10px var(--primary-cyan);
            width: 0%;
            transition: width 1.2s cubic-bezier(0.1, 0.5, 0.1, 1);
        }

        .section-title {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 1.25rem;
            color: var(--primary-cyan);
            margin-top: 0.8rem;
            margin-bottom: 0.8rem;
            border-bottom: 1px solid var(--border-glow);
            padding-bottom: 0.3rem;
        }

        .skills-container {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
        }

        .skill-tag {
            background: rgba(172, 169, 222, 0.12);
            border: 1px solid var(--primary-purple);
            color: #fff;
            padding: 6px 14px;
            border-radius: 8px;
            font-size: 0.88rem;
            transition: all 0.3s ease;
        }

        .skill-tag:hover {
            background: var(--primary-cyan);
            color: var(--bg-color);
            border-color: var(--primary-cyan);
            box-shadow: 0 0 15px var(--primary-cyan);
            transform: translateY(-2px);
        }

        /* About & Contact Card Details */
        .about-text {
            font-size: 0.95rem;
            line-height: 1.7;
            color: #e0ddf5;
            margin-bottom: 1.2rem;
        }

        .about-highlight {
            color: var(--primary-cyan);
            font-weight: 600;
        }

        .contact-list {
            display: flex;
            flex-direction: column;
            gap: 12px;
            margin-top: 1rem;
        }

        .contact-item {
            display: flex;
            align-items: center;
            gap: 15px;
            background: rgba(18, 14, 36, 0.6);
            border: 1px solid rgba(172, 169, 222, 0.2);
            padding: 12px 18px;
            border-radius: 12px;
            color: #fff;
            text-decoration: none;
            transition: all 0.3s ease;
        }

        .contact-item:hover {
            border-color: var(--primary-cyan);
            background: rgba(135, 232, 203, 0.1);
            transform: translateX(5px);
            box-shadow: 0 0 15px rgba(135, 232, 203, 0.2);
        }

        .contact-icon {
            font-size: 1.3rem;
            color: var(--primary-cyan);
        }

        .contact-info label {
            display: block;
            font-size: 0.75rem;
            color: var(--primary-purple);
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .contact-info span {
            font-size: 0.95rem;
            font-weight: 500;
        }

        /* Popups & Overlays */
        .dice-result-popup {
            position: fixed;
            top: 45%;
            left: 72%; /* แสดงเลขตรงกลางขวาเหนือเต๋า */
            transform: translate(-50%, -50%) scale(0);
            font-family: 'Cinzel', serif;
            font-size: 5rem;
            font-weight: 900;
            color: #fff;
            text-shadow: 0 0 30px var(--primary-cyan);
            pointer-events: none;
            z-index: 99;
            transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275), opacity 0.4s;
            opacity: 0;
        }

        .dice-result-popup.active {
            transform: translate(-50%, -50%) scale(1);
            opacity: 1;
        }

        .nat20-banner {
            position: fixed;
            top: 15%;
            left: 50%;
            transform: translate(-50%, -50%) scale(0);
            background: linear-gradient(135deg, #ffd700, #87e8cb);
            color: #0b0914;
            padding: 1rem 3.5rem;
            border-radius: 50px;
            font-family: 'Cinzel', serif;
            font-size: 2rem;
            font-weight: 900;
            box-shadow: 0 0 50px #ffd700;
            transition: transform 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            z-index: 100;
            pointer-events: none;
        }

        .nat20-banner.active {
            transform: translate(-50%, -50%) scale(1);
        }

        .interactive-hint {
            position: fixed;
            bottom: 25px;
            right: 25px;
            background: rgba(18, 14, 36, 0.85);
            border: 1px solid var(--primary-cyan);
            color: var(--primary-cyan);
            padding: 10px 20px;
            border-radius: 20px;
            font-size: 0.85rem;
            box-shadow: 0 0 20px rgba(135, 232, 203, 0.25);
            backdrop-filter: blur(10px);
            pointer-events: none;
            z-index: 10;
        }

        .audio-toggle {
            position: fixed;
            top: 25px;
            right: 25px;
            z-index: 10;
            background: var(--glass-bg);
            border: 1px solid var(--primary-cyan);
            color: var(--primary-cyan);
            padding: 8px 16px;
            border-radius: 20px;
            cursor: pointer;
            backdrop-filter: blur(8px);
            font-size: 0.85rem;
            transition: all 0.3s ease;
        }

        .audio-toggle:hover {
            background: var(--primary-cyan);
            color: var(--bg-color);
            box-shadow: 0 0 15px var(--primary-cyan);
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @media (max-width: 900px) {
            .page-wrapper {
                justify-content: center;
                padding: 1.5rem 1rem;
            }
            .dnd-card {
                max-width: 100%;
            }
            .dice-result-popup {
                left: 50%;
            }
        }
    </style>
</head>
<body>

    <canvas id="webgl-bg"></canvas>

    <button class="audio-toggle" id="audio-toggle">🔊 SFX: ON</button>

    <div class="dice-result-popup" id="dice-result">20</div>
    <div class="nat20-banner" id="nat20-banner">✨ CRITICAL SUCCESS! ✨</div>

    <div class="interactive-hint">
        🎲 คลิกทอยลูกเต๋า D20 ด้านขวา เพื่อสุ่มโชค!
    </div>

    <div class="page-wrapper">
        <div class="dnd-card" id="card">
            
            <!-- Navigation Menu -->
            <nav class="nav-menu">
                <button class="nav-btn active" onclick="switchTab('main')">⚔️ หน้าหลัก</button>
                <button class="nav-btn" onclick="switchTab('about')">📜 เกี่ยวกับ</button>
                <button class="nav-btn" onclick="switchTab('contact')">📬 ติดต่อ</button>
            </nav>

            <!-- 1. MAIN TAB (หน้าหลัก) -->
            <div id="tab-main" class="tab-content active">
                <div class="badge-container">
                    <span class="class-badge">LVL 4 ARTIST & DESIGNER</span>
                    <span class="class-badge">GAME & ANIMATION</span>
                    <span class="class-badge">MINECRAFT MODDER</span>
                </div>

                <div class="brand-title">bossu29</div>
                <hr class="divider-line">

                <h1>ศศิธร เซ้งรักษา</h1>
                <div class="subtitle">Sasithorn Sengraksa | 3D & Digital Artist Portfolio</div>

                <div class="info-grid">
                    <div class="info-box">
                        <h3>📜 การศึกษา (Education)</h3>
                        <p><strong>มทร.รัตนโกสินทร์ ศาลายา</strong></p>
                        <p>คณะสถาปัตยกรรมศาสตร์ และการออกแบบ</p>
                        <p>สาขาเกมและอนิเมชั่น (ชั้นปีที่ 4)</p>
                    </div>
                    
                    <div class="info-box">
                        <h3>🎨 ความเชี่ยวชาญ (Focus Area)</h3>
                        <p>ออกแบบตัวละคร (Character Design), วาดภาพประกอบ (Illustration), 3D Assets & Modding</p>
                    </div>
                </div>

                <div class="section-title">📊 สเตตัสและความชำนาญ (Stats)</div>
                <div class="stats-section">
                    <div class="stat-bar-container">
                        <div class="stat-header"><span>Character Design & Concept Art</span><span>98%</span></div>
                        <div class="stat-bar-bg"><div class="stat-bar-fill" data-width="98%"></div></div>
                    </div>
                    <div class="stat-bar-container">
                        <div class="stat-header"><span>Digital Illustration</span><span>92%</span></div>
                        <div class="stat-bar-bg"><div class="stat-bar-fill" data-width="92%"></div></div>
                    </div>
                    <div class="stat-bar-container">
                        <div class="stat-header"><span>3D Modeling & Assets (Blender / Unity)</span><span>88%</span></div>
                        <div class="stat-bar-bg"><div class="stat-bar-fill" data-width="88%"></div></div>
                    </div>
                    <div class="stat-bar-container">
                        <div class="stat-header"><span>Minecraft World Building & Figura Modding</span><span>95%</span></div>
                        <div class="stat-bar-bg"><div class="stat-bar-fill" data-width="95%"></div></div>
                    </div>
                </div>
            </div>

            <!-- 2. ABOUT TAB (เกี่ยวกับ) -->
            <div id="tab-about" class="tab-content">
                <div class="brand-title">ABOUT ME</div>
                <hr class="divider-line">

                <p class="about-text">
                    สวัสดีครับ! ผม <span class="about-highlight">ศศิธร เซ้งรักษา (bossu29)</span> นศ. ชั้นปีที่ 4 สาขาเกมและอนิเมชั่น ผู้หลงใหลในโลกของ 3D Art, การออกแบบตัวละคร และระบบเกม RPG / Fantasy 🏰
                </p>
                <p class="about-text">
                    ผมชอบถ่ายทอดจินตนาการผ่านงานวาดและโมเดล 3 มิติ ไม่ว่าจะเป็นแนว <span class="about-highlight">Cyberpunk, Fantasy หรือ D&D World</span> รวมไปถึงการม็อดเกม Custom อวตารและแอนิเมชันผ่าน <span class="about-highlight">Minecraft Figura Modding</span>
                </p>

                <div class="section-title">🛠️ เครื่องมือที่ใช้ (Tools & Software)</div>
                <div class="skills-container">
                    <div class="skill-tag">🎨 Clip Studio Paint / Photoshop</div>
                    <div class="skill-tag">🧊 Blender 3D</div>
                    <div class="skill-tag">🕹️ Unity Engine</div>
                    <div class="skill-tag">🦊 Figura API (Minecraft)</div>
                    <div class="skill-tag">📐 Blockbench</div>
                </div>
            </div>

            <!-- 3. CONTACT TAB (ติดต่อ) -->
            <div id="tab-contact" class="tab-content">
                <div class="brand-title">GET IN TOUCH</div>
                <hr class="divider-line">

                <p class="about-text">ยินดีรับงาน Freelance, Contract งานออกแบบตัวละคร, 3D Assets และงานทำ Mod มายคราฟครับ สามารถติดต่อได้ผ่านช่องทางด้านล่างนี้ได้เลยครับ ✨</p>

                <div class="contact-list">
                    <a href="mailto:sasithorn.seng@gmail.com" class="contact-item">
                        <span class="contact-icon">✉️</span>
                        <div class="contact-info">
                            <label>Email</label>
                            <span>sasithorn.seng@gmail.com</span>
                        </div>
                    </a>

                    <a href="#" class="contact-item" onclick="return false;">
                        <span class="contact-icon">💬</span>
                        <div class="contact-info">
                            <label>Discord</label>
                            <span>bossu29#0000</span>
                        </div>
                    </a>

                    <a href="#" class="contact-item" onclick="return false;">
                        <span class="contact-icon">🎨</span>
                        <div class="contact-info">
                            <label>ArtStation / Portfolio</label>
                            <span>artstation.com/bossu29</span>
                        </div>
                    </a>

                    <a href="#" class="contact-item" onclick="return false;">
                        <span class="contact-icon">🌐</span>
                        <div class="contact-info">
                            <label>Location</label>
                            <span>นครปฐม / กรุงเทพฯ, ประเทศไทย</span>
                        </div>
                    </a>
                </div>
            </div>

        </div>
    </div>

    <script>
        // --- 0. TAB SWITCHING SYSTEM ---
        function switchTab(tabName) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            document.querySelectorAll('.nav-btn').forEach(el => el.classList.remove('active'));

            document.getElementById(`tab-${tabName}`).classList.add('active');
            event.currentTarget.classList.add('active');

            // Trigger Stat bar animation if switching to main tab
            if(tabName === 'main') {
                document.querySelectorAll('.stat-bar-fill').forEach(bar => {
                    bar.style.width = '0%';
                    setTimeout(() => bar.style.width = bar.getAttribute('data-width'), 50);
                });
            }
        }

        // --- 1. SYNTHESIZED SOUND EFFECTS (Web Audio API) ---
        let soundEnabled = true;
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

        document.getElementById('audio-toggle').addEventListener('click', (e) => {
            soundEnabled = !soundEnabled;
            e.target.innerText = soundEnabled ? '🔊 SFX: ON' : '🔇 SFX: OFF';
        });

        function playRollSound() {
            if (!soundEnabled) return;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'triangle';
            osc.frequency.setValueAtTime(200 + Math.random() * 400, audioCtx.currentTime);
            osc.frequency.exponentialRampToValueAtTime(100, audioCtx.currentTime + 0.08);
            gain.gain.setValueAtTime(0.15, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.08);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start();
            osc.stop(audioCtx.currentTime + 0.08);
        }

        function playWinSound() {
            if (!soundEnabled) return;
            const now = audioCtx.currentTime;
            [523.25, 659.25, 783.99, 1046.50].forEach((freq, i) => {
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(freq, now + i * 0.1);
                gain.gain.setValueAtTime(0.2, now + i * 0.1);
                gain.gain.exponentialRampToValueAtTime(0.001, now + i * 0.1 + 0.4);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now + i * 0.1);
                osc.stop(now + i * 0.1 + 0.4);
            });
        }

        // --- 2. THREE.JS SCENE SETUP ---
        const scene = new THREE.Scene();
        scene.fog = new THREE.FogExp2(0x080612, 0.03);

        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 5;

        const renderer = new THREE.WebGLRenderer({
            canvas: document.querySelector('#webgl-bg'),
            antialias: true
        });
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.setSize(window.innerWidth, window.innerHeight);

        // --- 3. LIGHTING ---
        const ambientLight = new THREE.AmbientLight(0x1a152b, 2.2);
        scene.add(ambientLight);

        const cyanLight = new THREE.PointLight(0x87e8cb, 3.5, 25);
        cyanLight.position.set(6, 4, 3);
        scene.add(cyanLight);

        const purpleLight = new THREE.PointLight(0xaca9de, 3.5, 25);
        purpleLight.position.set(-6, -4, 3);
        scene.add(purpleLight);

        // --- 4. D20 DICE & TEXTURE SYSTEM ---
        const d20Textures = {};
        function getOrCreateNumberTexture(number) {
            if (d20Textures[number]) return d20Textures[number];

            const canvas = document.createElement('canvas');
            canvas.width = 256;
            canvas.height = 256;
            const ctx = canvas.getContext('2d');

            ctx.fillStyle = '#aca9de';
            ctx.fillRect(0, 0, 256, 256);

            ctx.strokeStyle = '#87e8cb';
            ctx.lineWidth = 16;
            ctx.strokeRect(10, 10, 236, 236);

            ctx.fillStyle = '#080612';
            ctx.font = 'Bold 115px Cinzel, sans-serif';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText(number.toString(), 128, 128);

            const texture = new THREE.CanvasTexture(canvas);
            d20Textures[number] = texture;
            return texture;
        }

        const d20Material = new THREE.MeshStandardMaterial({
            map: getOrCreateNumberTexture(20),
            roughness: 0.25,
            metalness: 0.5,
            flatShading: true
        });

        // ปรับขนาด D20 ให้ใหญ่เด่นขึ้นตรงกลางขวา (1.15)
        const d20Geo = new THREE.IcosahedronGeometry(1.15, 0);
        const d20Mesh = new THREE.Mesh(d20Geo, d20Material);
        
        const d20Wire = new THREE.Mesh(
            d20Geo,
            new THREE.MeshBasicMaterial({ color: 0x87e8cb, wireframe: true })
        );
        d20Wire.scale.setScalar(1.02);

        const d20Hitbox = new THREE.Mesh(
            new THREE.SphereGeometry(1.4, 8, 8),
            new THREE.MeshBasicMaterial({ visible: false })
        );

        const d20Group = new THREE.Group();
        d20Group.add(d20Mesh);
        d20Group.add(d20Wire);
        d20Group.add(d20Hitbox);
        scene.add(d20Group);

        // --- 5. FIREWORKS BURST FOR NAT 20 ---
        const fwCount = 300;
        const fwGeo = new THREE.BufferGeometry();
        const fwPos = new Float32Array(fwCount * 3);
        const fwVel = [];

        for(let i = 0; i < fwCount; i++) {
            fwPos[i*3] = 0;
            fwPos[i*3+1] = 0;
            fwPos[i*3+2] = 0;

            const theta = Math.random() * Math.PI * 2;
            const phi = Math.random() * Math.PI;
            const speed = 0.12 + Math.random() * 0.18;

            fwVel.push({
                x: speed * Math.sin(phi) * Math.cos(theta),
                y: speed * Math.sin(phi) * Math.sin(theta),
                z: speed * Math.cos(phi)
            });
        }

        fwGeo.setAttribute('position', new THREE.BufferAttribute(fwPos, 3));
        const fwMat = new THREE.PointsMaterial({
            color: 0xffd700,
            size: 0.12,
            transparent: true,
            opacity: 0
        });
        const fireworks = new THREE.Points(fwGeo, fwMat);
        scene.add(fireworks);

        let isFWActive = false;
        let fwTimer = 0;

        function triggerFireworks() {
            isFWActive = true;
            fwTimer = 0;
            fwMat.opacity = 1;
            const pos = fireworks.geometry.attributes.position.array;
            for(let i = 0; i < fwCount * 3; i++) pos[i] = 0;
            fireworks.position.copy(d20Group.position);
            fireworks.geometry.attributes.position.needsUpdate = true;
        }

        // --- 6. FLOATING DUST PARTICLES & MAGIC RINGS ---
        const bgGroup = new THREE.Group();
        const ringGeo = new THREE.TorusGeometry(3.0, 0.015, 16, 100);
        const ringMat = new THREE.MeshBasicMaterial({ color: 0x87e8cb, wireframe: true });
        const ring = new THREE.Mesh(ringGeo, ringMat);
        ring.rotation.x = Math.PI / 3;
        bgGroup.add(ring);
        scene.add(bgGroup);

        const pCount = 650;
        const pPos = new Float32Array(pCount * 3);
        for(let i = 0; i < pCount * 3; i++) {
            pPos[i] = (Math.random() - 0.5) * 18;
        }
        const pGeo = new THREE.BufferGeometry();
        pGeo.setAttribute('position', new THREE.BufferAttribute(pPos, 3));
        const pMat = new THREE.PointsMaterial({ size: 0.035, color: 0xaca9de, transparent: true, opacity: 0.5 });
        const pMesh = new THREE.Points(pGeo, pMat);
        scene.add(pMesh);

        // --- 7. POSITIONING & RESPONSIVE (วาง D20 ตรงกลางฝั่งขวา) ---
        function updatePositions() {
            const aspect = window.innerWidth / window.innerHeight;
            const vFOV = THREE.MathUtils.degToRad(camera.fov);
            const height = 2 * Math.tan(vFOV / 2) * camera.position.z;
            const width = height * aspect;

            if (window.innerWidth > 900) {
                // วาง D20 ฝั่งขวาตรงกลาง
                d20Group.position.set(width / 4 + 0.5, 0, 0);
            } else {
                // สำหรับมือถือ วางมุมขวาบน
                d20Group.position.set(width / 2 - 1.0, height / 2 - 1.2, 0);
            }
        }
        updatePositions();

        // --- 8. DICE ROLL LOGIC & INTERACTION ---
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();
        let isRolling = false;

        function showResultPopup(value) {
            const popup = document.getElementById('dice-result');
            const banner = document.getElementById('nat20-banner');
            popup.innerText = value;
            
            if(value === 20) {
                popup.style.color = '#ffd700';
                popup.style.textShadow = '0 0 40px #ffd700';
                banner.classList.add('active');
                triggerFireworks();
                playWinSound();
                setTimeout(() => banner.classList.remove('active'), 3000);
            } else if(value === 1) {
                popup.style.color = '#ff4757';
                popup.style.textShadow = '0 0 30px #ff4757';
            } else {
                popup.style.color = '#87e8cb';
                popup.style.textShadow = '0 0 30px #87e8cb';
            }

            popup.classList.add('active');
            setTimeout(() => popup.classList.remove('active'), 1200);
        }

        function rollDice() {
            if (isRolling) return;
            if (audioCtx.state === 'suspended') audioCtx.resume();
            
            isRolling = true;
            let finalValue = Math.floor(Math.random() * 20) + 1;

            const rollInterval = setInterval(() => {
                let tempVal = Math.floor(Math.random() * 20) + 1;
                d20Material.map = getOrCreateNumberTexture(tempVal);
                d20Material.needsUpdate = true;
                playRollSound();
            }, 60);

            // GSAP 3D Spin Animation
            gsap.to(d20Group.rotation, {
                x: d20Group.rotation.x + Math.PI * 6 + Math.random(),
                y: d20Group.rotation.y + Math.PI * 6 + Math.random(),
                z: d20Group.rotation.z + Math.PI * 4,
                duration: 1.2,
                ease: 'power2.out',
                onComplete: () => {
                    clearInterval(rollInterval);
                    d20Material.map = getOrCreateNumberTexture(finalValue);
                    d20Material.needsUpdate = true;
                    isRolling = false;
                    showResultPopup(finalValue);
                }
            });
        }

        window.addEventListener('pointerdown', (e) => {
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersectsD20 = raycaster.intersectObjects([d20Mesh, d20Wire, d20Hitbox]);

            if (intersectsD20.length > 0) rollDice();
        });

        // Mouse Hover & Card 3D Tilt Effect
        const card = document.getElementById('card');
        window.addEventListener('pointermove', (e) => {
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObjects([d20Mesh, d20Wire, d20Hitbox]);
            document.body.style.cursor = intersects.length > 0 ? 'pointer' : 'default';

            // 3D Card Tilt
            const rx = (e.clientY / window.innerHeight - 0.5) * -10;
            const ry = (e.clientX / window.innerWidth - 0.5) * 10;
            card.style.transform = `rotateX(${rx}deg) rotateY(${ry}deg)`;
        });

        // --- 9. ANIMATION LOOP ---
        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);
            const elapsedTime = clock.getElapsedTime();

            // D20 Rotation Animation
            if (!isRolling) {
                d20Group.rotation.x = Math.sin(elapsedTime * 0.5) * 0.2;
                d20Group.rotation.y = elapsedTime * 0.4;
            }

            // Fireworks Animation
            if (isFWActive) {
                fwTimer += 0.016;
                const pos = fireworks.geometry.attributes.position.array;
                for (let i = 0; i < fwCount; i++) {
                    pos[i * 3] += fwVel[i].x;
                    pos[i * 3 + 1] += fwVel[i].y;
                    pos[i * 3 + 2] += fwVel[i].z;
                }
                fireworks.geometry.attributes.position.needsUpdate = true;

                if (fwTimer > 1.5) {
                    fwMat.opacity -= 0.02;
                    if (fwMat.opacity <= 0) isFWActive = false;
                }
            }

            // Background rotation
            ring.rotation.z = elapsedTime * 0.1;
            pMesh.rotation.y = elapsedTime * 0.03;

            renderer.render(scene, camera);
        }

        animate();

        // Animate Stat Bars on load
        window.addEventListener('load', () => {
            setTimeout(() => {
                document.querySelectorAll('.stat-bar-fill').forEach(bar => {
                    bar.style.width = bar.getAttribute('data-width');
                });
            }, 300);
        });

        // Window Resize
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
            updatePositions();
        });
    </script>
</body>
</html>
