<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sasithorn S. | Minimal D&D Portfolio</title>
    
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;450;600&family=Prompt:wght@300;400;500&display=swap" rel="stylesheet">

    <!-- Three.js & GSAP -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>

    <style>
        :root {
            --bg-base: #f7f6f2;
            --glass-bg: rgba(255, 255, 255, 0.82);
            --glass-border: rgba(0, 0, 0, 0.06);
            --accent-charcoal: #1c1c1e;
            --accent-gold: #c59b27;
            --text-main: #2c2c2e;
            --text-sub: #6c6c70;
            --card-radius: 20px;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Plus Jakarta Sans', 'Prompt', sans-serif;
            user-select: none;
        }

        body {
            background-color: var(--bg-base);
            color: var(--text-main);
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

        .container {
            position: relative;
            z-index: 2;
            width: 100%;
            max-width: 1300px;
            margin: 0 auto;
            padding: 2.5rem 2rem;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: flex-start;
            pointer-events: none;
            perspective: 1200px;
        }

        /* MINIMAL GLASS CARD */
        .dnd-card {
            pointer-events: auto;
            background: var(--glass-bg);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border: 1px solid var(--glass-border);
            border-radius: var(--card-radius);
            padding: 2.4rem;
            width: 100%;
            max-width: 580px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.04),
                        0 1px 3px rgba(0, 0, 0, 0.02);
            position: relative;
            transition: transform 0.2s cubic-bezier(0.2, 0, 0.2, 1);
            transform-style: preserve-3d;
        }

        .card-header-flex {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 1.2rem;
        }

        .card-tag {
            font-size: 0.72rem;
            letter-spacing: 1.5px;
            text-transform: uppercase;
            color: var(--text-sub);
            font-weight: 600;
        }

        .spellbook-trigger {
            background: transparent;
            border: 1px solid var(--glass-border);
            color: var(--text-main);
            padding: 5px 12px;
            border-radius: 20px;
            font-size: 0.75rem;
            cursor: pointer;
            transition: all 0.2s ease;
            font-weight: 500;
        }

        .spellbook-trigger:hover {
            border-color: var(--accent-charcoal);
            background: var(--accent-charcoal);
            color: #fff;
        }

        /* TAB NAVIGATION */
        .tab-nav {
            display: flex;
            gap: 6px;
            margin-bottom: 1.5rem;
            background: rgba(0, 0, 0, 0.03);
            padding: 4px;
            border-radius: 12px;
        }

        .tab-btn {
            flex: 1;
            background: transparent;
            border: none;
            color: var(--text-sub);
            padding: 8px 12px;
            border-radius: 8px;
            font-size: 0.82rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .tab-btn.active {
            background: #fff;
            color: var(--text-main);
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
            font-weight: 600;
        }

        .tab-content {
            display: none;
            animation: softFadeIn 0.3s ease;
        }

        .tab-content.active {
            display: block;
        }

        .brand-title {
            font-size: 2rem;
            font-weight: 700;
            letter-spacing: -0.5px;
            color: var(--text-main);
            line-height: 1.1;
            margin-bottom: 0.2rem;
        }

        .subtitle-th {
            font-size: 1.05rem;
            color: var(--text-main);
            font-weight: 500;
            margin-bottom: 0.2rem;
        }

        .subtitle-en {
            font-size: 0.8rem;
            color: var(--text-sub);
            margin-bottom: 1.2rem;
            font-weight: 300;
        }

        .info-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 0.8rem;
            margin-bottom: 1.2rem;
        }

        .info-box {
            background: rgba(255, 255, 255, 0.5);
            border: 1px solid var(--glass-border);
            padding: 1rem;
            border-radius: 14px;
            transition: all 0.2s ease;
        }

        .info-box:hover {
            border-color: rgba(0, 0, 0, 0.12);
        }

        .info-box h3 {
            font-size: 0.72rem;
            color: var(--text-sub);
            margin-bottom: 0.3rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            font-weight: 600;
        }

        .info-box p {
            font-size: 0.8rem;
            color: var(--text-main);
            line-height: 1.4;
        }

        .section-header {
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-top: 1.2rem;
            margin-bottom: 0.6rem;
        }

        .section-title {
            font-size: 0.85rem;
            color: var(--text-main);
            font-weight: 600;
            letter-spacing: 0.5px;
        }

        .roll-btn {
            background: transparent;
            border: 1px solid var(--glass-border);
            color: var(--text-main);
            padding: 4px 10px;
            border-radius: 12px;
            font-size: 0.72rem;
            cursor: pointer;
            transition: all 0.2s ease;
            font-weight: 500;
        }

        .roll-btn:hover {
            background: var(--text-main);
            color: #fff;
        }

        .stat-bar-container {
            margin-bottom: 0.7rem;
        }

        .stat-header {
            display: flex;
            justify-content: space-between;
            font-size: 0.75rem;
            color: var(--text-sub);
            margin-bottom: 0.2rem;
        }

        .stat-bar-bg {
            width: 100%;
            height: 5px;
            background: rgba(0, 0, 0, 0.04);
            border-radius: 10px;
            overflow: hidden;
        }

        .stat-bar-fill {
            height: 100%;
            background: var(--text-main);
            border-radius: 10px;
            width: 0%;
            transition: width 1s cubic-bezier(0.16, 1, 0.3, 1);
        }

        .skills-container {
            display: flex;
            flex-wrap: wrap;
            gap: 6px;
            margin-top: 0.7rem;
        }

        .skill-tag {
            background: rgba(0, 0, 0, 0.03);
            border: 1px solid var(--glass-border);
            color: var(--text-main);
            padding: 5px 12px;
            border-radius: 10px;
            font-size: 0.75rem;
            transition: all 0.2s ease;
        }

        .skill-tag:hover {
            background: var(--text-main);
            color: #fff;
        }

        /* SPELLBOOK MODAL */
        .spellbook-modal {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(247, 246, 242, 0.96);
            backdrop-filter: blur(20px);
            border-radius: var(--card-radius);
            padding: 2rem;
            z-index: 10;
            display: none;
            opacity: 0;
            transition: opacity 0.25s ease;
            overflow-y: auto;
        }

        .spellbook-modal.active {
            display: block;
            opacity: 1;
        }

        .modal-close {
            position: absolute;
            top: 18px;
            right: 18px;
            background: rgba(0, 0, 0, 0.04);
            border: none;
            color: var(--text-main);
            width: 28px;
            height: 28px;
            border-radius: 50%;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s ease;
        }

        .modal-close:hover {
            background: var(--text-main);
            color: #fff;
        }

        .spell-card {
            background: #fff;
            border: 1px solid var(--glass-border);
            border-radius: 14px;
            padding: 0.9rem 1.1rem;
            margin-bottom: 0.8rem;
            box-shadow: 0 2px 6px rgba(0, 0, 0, 0.02);
        }

        .spell-card h4 {
            font-size: 0.9rem;
            color: var(--text-main);
            margin-bottom: 3px;
            display: flex;
            justify-content: space-between;
            font-weight: 600;
        }

        .spell-card p {
            font-size: 0.78rem;
            color: var(--text-sub);
            line-height: 1.4;
        }

        /* CONTACT */
        .contact-list {
            display: flex;
            flex-direction: column;
            gap: 0.8rem;
            margin-top: 1rem;
        }

        .contact-item {
            display: flex;
            align-items: center;
            gap: 14px;
            background: rgba(255, 255, 255, 0.5);
            border: 1px solid var(--glass-border);
            padding: 0.9rem 1.2rem;
            border-radius: 14px;
            text-decoration: none;
            color: var(--text-main);
            transition: all 0.2s ease;
        }

        .contact-item:hover {
            border-color: rgba(0, 0, 0, 0.15);
            transform: translateX(4px);
        }

        .contact-icon {
            font-size: 1rem;
            background: rgba(0, 0, 0, 0.03);
            width: 36px;
            height: 36px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 10px;
        }

        .contact-text h4 {
            font-size: 0.75rem;
            color: var(--text-sub);
            font-weight: 600;
            margin-bottom: 2px;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .contact-text p {
            font-size: 0.82rem;
            color: var(--text-main);
        }

        /* POPUPS & TOASTS */
        .dice-result-popup {
            position: fixed;
            top: 45%;
            right: 25%;
            transform: translate(50%, -50%) scale(0);
            font-size: 4.5rem;
            font-weight: 700;
            color: var(--text-main);
            pointer-events: none;
            z-index: 99;
            transition: transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275), opacity 0.3s;
            opacity: 0;
        }

        .dice-result-popup.active {
            transform: translate(50%, -50%) scale(1);
            opacity: 1;
        }

        .skill-toast {
            position: fixed;
            bottom: 25px;
            left: 50%;
            transform: translateX(-50%) translateY(20px);
            background: #fff;
            border: 1px solid var(--glass-border);
            color: var(--text-main);
            padding: 8px 20px;
            border-radius: 20px;
            font-size: 0.85rem;
            box-shadow: 0 10px 25px rgba(0,0,0,0.06);
            opacity: 0;
            pointer-events: none;
            transition: all 0.25s ease;
            z-index: 90;
            font-weight: 500;
        }

        .skill-toast.active {
            opacity: 1;
            transform: translateX(-50%) translateY(0);
        }

        .interactive-hint {
            position: fixed;
            bottom: 25px;
            right: 25px;
            background: rgba(255, 255, 255, 0.8);
            border: 1px solid var(--glass-border);
            color: var(--text-sub);
            padding: 6px 14px;
            border-radius: 20px;
            font-size: 0.75rem;
            backdrop-filter: blur(10px);
            pointer-events: none;
            z-index: 10;
        }

        .audio-toggle {
            position: fixed;
            top: 25px;
            right: 25px;
            z-index: 10;
            background: rgba(255, 255, 255, 0.8);
            border: 1px solid var(--glass-border);
            color: var(--text-sub);
            padding: 6px 14px;
            border-radius: 20px;
            cursor: pointer;
            backdrop-filter: blur(10px);
            font-size: 0.75rem;
            transition: all 0.2s ease;
        }

        .audio-toggle:hover {
            color: var(--text-main);
            border-color: rgba(0, 0, 0, 0.15);
        }

        @keyframes softFadeIn {
            from { opacity: 0; transform: translateY(6px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @media (max-width: 992px) {
            .container {
                justify-content: center;
                padding: 1.5rem 1rem;
            }
            .dnd-card {
                max-width: 100%;
            }
            .dice-result-popup {
                right: 50%;
                transform: translate(50%, -50%) scale(0);
            }
        }
    </style>
</head>
<body>

    <canvas id="webgl-bg"></canvas>

    <button class="audio-toggle" id="audio-toggle">SOUND: ON</button>

    <div class="dice-result-popup" id="dice-result">20</div>
    <div class="skill-toast" id="skill-toast">Arcane Check</div>

    <div class="interactive-hint">
        🎲 คลิกที่ลูกเต๋า 3D ขวามือเพื่อทอย D20
    </div>

    <div class="container">
        <div class="dnd-card" id="card">
            
            <div class="card-header-flex">
                <div class="card-tag">Character Sheet // D&D Minimal</div>
                <button class="spellbook-trigger" onclick="toggleSpellbook(true)">
                    Spellbook ↗
                </button>
            </div>

            <!-- TAB NAVIGATION -->
            <div class="tab-nav">
                <button class="tab-btn active" onclick="switchTab('home', this)">Profile</button>
                <button class="tab-btn" onclick="switchTab('about', this)">About</button>
                <button class="tab-btn" onclick="switchTab('contact', this)">Contact</button>
            </div>

            <!-- TAB 1: HOME PROFILE -->
            <div class="tab-content active" id="tab-home">
                <div class="brand-title">bossu29</div>
                <div class="subtitle-th">นางสาว ศศิธร เซ้งรักษา</div>
                <div class="subtitle-en">Sasithorn Sengraksa | 3D & Character Artist</div>

                <div class="info-grid">
                    <div class="info-box">
                        <h3>Education</h3>
                        <p><strong>RMUTT Salaya</strong></p>
                        <p>Game & Animation (Year 4)</p>
                    </div>
                    
                    <div class="info-box">
                        <h3>Focus</h3>
                        <p><strong>Character Design</strong></p>
                        <p>Illustration & Minecraft Custom Modding</p>
                    </div>
                </div>

                <div class="section-header">
                    <div class="section-title">Stats & Mastery</div>
                    <button class="roll-btn" onclick="rollSkillCheck()">Roll Skill</button>
                </div>

                <div class="stats-section">
                    <div class="stat-bar-container">
                        <div class="stat-header"><span>Character Design & Concepts</span><span>98%</span></div>
                        <div class="stat-bar-bg"><div class="stat-bar-fill" data-width="98%"></div></div>
                    </div>
                    <div class="stat-bar-container">
                        <div class="stat-header"><span>Digital Illustration</span><span>92%</span></div>
                        <div class="stat-bar-bg"><div class="stat-bar-fill" data-width="92%"></div></div>
                    </div>
                    <div class="stat-bar-container">
                        <div class="stat-header"><span>3D Asset Modeling (Blender / Unity)</span><span>88%</span></div>
                        <div class="stat-bar-bg"><div class="stat-bar-fill" data-width="88%"></div></div>
                    </div>
                    <div class="stat-bar-container">
                        <div class="stat-header"><span>Minecraft World & Figura Modding</span><span>95%</span></div>
                        <div class="stat-bar-bg"><div class="stat-bar-fill" data-width="95%"></div></div>
                    </div>
                </div>

                <div class="skills-container">
                    <div class="skill-tag">Digital Art</div>
                    <div class="skill-tag">Character Design</div>
                    <div class="skill-tag">Illustration</div>
                    <div class="skill-tag">Minecraft World Building</div>
                    <div class="skill-tag">Figura Modding</div>
                    <div class="skill-tag">Game Assets</div>
                </div>
            </div>

            <!-- TAB 2: ABOUT -->
            <div class="tab-content" id="tab-about">
                <div class="brand-title">About</div>
                <div class="subtitle-en">Background & Vision</div>

                <div class="info-box" style="margin-bottom: 0.8rem;">
                    <h3>Biography</h3>
                    <p>สวัสดีค่ะ ศศิธร เซ้งรักษา (bossu29) นักศึกษาชั้นปีที่ 4 สาขาเกมและอนิเมชั่น สนใจการออกแบบตัวละคร งานปั้น 3D และการสร้างสรรค์คอนเทนต์คัสตอมในเกม Minecraft</p>
                </div>
                
                <div class="info-box" style="margin-bottom: 0.8rem;">
                    <h3>Vision</h3>
                    <p>มุ่งเน้นการสร้างสรรค์งานดีไซน์ที่มีเอกลักษณ์ ถ่ายทอดเรื่องราวผ่านคาแรคเตอร์ และพัฒนาชิ้นงานให้ตอบโจทย์อุตสาหกรรมเกมอย่างมีคุณภาพ</p>
                </div>

                <div class="section-title" style="margin-bottom: 0.5rem;">Tools & Software</div>
                <div class="skills-container">
                    <div class="skill-tag">Blender 3D</div>
                    <div class="skill-tag">Unity Engine</div>
                    <div class="skill-tag">Clip Studio Paint</div>
                    <div class="skill-tag">Photoshop</div>
                    <div class="skill-tag">Blockbench</div>
                </div>
            </div>

            <!-- TAB 3: CONTACT -->
            <div class="tab-content" id="tab-contact">
                <div class="brand-title">Contact</div>
                <div class="subtitle-en">Get in touch</div>
                
                <div class="contact-list">
                    <a href="mailto:sengraksa2005@gmail.com" class="contact-item">
                        <div class="contact-icon">✉</div>
                        <div class="contact-text">
                            <h4>Email</h4>
                            <p>sengraksa2005@gmail.com</p>
                        </div>
                    </a>

                    <div class="contact-item">
                        <div class="contact-icon">#</div>
                        <div class="contact-text">
                            <h4>Discord</h4>
                            <p>bossu_u</p>
                        </div>
                    </div>

                    <a href="#" class="contact-item">
                        <div class="contact-icon">↗</div>
                        <div class="contact-text">
                            <h4>Twitter / X</h4>
                            <p>@bossu29_art</p>
                        </div>
                    </a>
                </div>
            </div>

            <!-- SPELLBOOK MODAL -->
            <div class="spellbook-modal" id="spellbook-modal">
                <button class="modal-close" onclick="toggleSpellbook(false)">✕</button>
                <div class="brand-title" style="font-size: 1.6rem; margin-bottom: 0.2rem;">Spellbook</div>
                <div class="subtitle-en" style="margin-bottom: 1rem;">Abilities & Expertise</div>

                <div class="spell-card">
                    <h4><span>Character Crafting</span> <span>RANK S+</span></h4>
                    <p>ออกแบบคาแรคเตอร์และจัดทำเอกสาร Character Sheet Deconstruction อย่างละเอียดรอบด้าน</p>
                </div>

                <div class="spell-card">
                    <h4><span>Figura Avatar Alchemy</span> <span>RANK S</span></h4>
                    <p>ปรับแต่งอวตารและระบบ Particle ใน Minecraft ด้วย Global API และ Custom Scripting</p>
                </div>

                <div class="spell-card">
                    <h4><span>3D Asset Pipeline</span> <span>RANK A+</span></h4>
                    <p>ส่งออกและเชื่อมโยงโมเดล 3D จาก Blender สู่ Game Engines พร้อมเซ็ตอัพ Material และ Rigging</p>
                </div>
            </div>

        </div>
    </div>

    <script>
        // --- MODAL CONTROLS ---
        function toggleSpellbook(show) {
            const modal = document.getElementById('spellbook-modal');
            if (show) {
                modal.classList.add('active');
                playSoftNote(400);
            } else {
                modal.classList.remove('active');
                playSoftNote(250);
            }
        }

        // --- SKILL CHECK ROLLER ---
        function rollSkillCheck() {
            const roll = Math.floor(Math.random() * 20) + 1;
            const toast = document.getElementById('skill-toast');
            let statusText = `Rolled ${roll} + 6 = ${roll+6} (Success)`;

            if (roll === 20) statusText = 'Natural 20! Critical Success!';
            
            toast.innerText = statusText;
            toast.classList.add('active');
            setTimeout(() => toast.classList.remove('active'), 2500);
            playSoftNote(300 + roll * 15);
        }

        // --- TAB SYSTEM ---
        function switchTab(tabName, btnElement) {
            document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
            document.querySelectorAll('.tab-content').forEach(content => content.classList.remove('active'));

            btnElement.classList.add('active');
            document.getElementById(`tab-${tabName}`).classList.add('active');
            playSoftNote(350);

            if(tabName === 'home') {
                document.querySelectorAll('.stat-bar-fill').forEach(bar => {
                    bar.style.width = '0%';
                    setTimeout(() => bar.style.width = bar.getAttribute('data-width'), 50);
                });
            }
        }

        // --- AUDIO ENGINE ---
        let soundEnabled = true;
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

        document.getElementById('audio-toggle').addEventListener('click', (e) => {
            soundEnabled = !soundEnabled;
            e.target.innerText = soundEnabled ? 'SOUND: ON' : 'SOUND: OFF';
        });

        function playSoftNote(freq = 300) {
            if (!soundEnabled) return;
            if (audioCtx.state === 'suspended') audioCtx.resume();
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'sine';
            osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
            gain.gain.setValueAtTime(0.03, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + 0.3);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start();
            osc.stop(audioCtx.currentTime + 0.3);
        }

        // --- THREE.JS MINIMAL SCENE ---
        const scene = new THREE.Scene();

        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 4.5;

        const renderer = new THREE.WebGLRenderer({
            canvas: document.querySelector('#webgl-bg'),
            antialias: true,
            alpha: true
        });
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.setSize(window.innerWidth, window.innerHeight);

        // --- LIGHTS ---
        const ambientLight = new THREE.AmbientLight(0xffffff, 1.2);
        scene.add(ambientLight);

        const pointLight = new THREE.PointLight(0xc59b27, 2, 20);
        pointLight.position.set(3, 3, 3);
        scene.add(pointLight);

        // --- CRYSTAL D20 DICE ---
        const d20Textures = {};
        function getNumberTexture(number) {
            if (d20Textures[number]) return d20Textures[number];

            const canvas = document.createElement('canvas');
            canvas.width = 256;
            canvas.height = 256;
            const ctx = canvas.getContext('2d');

            ctx.fillStyle = '#ffffff';
            ctx.fillRect(0, 0, 256, 256);

            ctx.strokeStyle = '#1c1c1e';
            ctx.lineWidth = 10;
            ctx.strokeRect(16, 16, 224, 224);

            ctx.fillStyle = '#1c1c1e';
            ctx.font = '700 100px Plus Jakarta Sans, sans-serif';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText(number.toString(), 128, 130);

            const texture = new THREE.CanvasTexture(canvas);
            d20Textures[number] = texture;
            return texture;
        }

        const d20Geo = new THREE.IcosahedronGeometry(1.1, 0);
        const d20Material = new THREE.MeshPhysicalMaterial({
            map: getNumberTexture(20),
            roughness: 0.15,
            metalness: 0.05,
            transmission: 0.85,
            opacity: 0.92,
            transparent: true,
            ior: 1.5
        });

        const d20Mesh = new THREE.Mesh(d20Geo, d20Material);
        const d20Wire = new THREE.Mesh(
            d20Geo,
            new THREE.MeshBasicMaterial({ color: 0x1c1c1e, wireframe: true, transparent: true, opacity: 0.15 })
        );
        d20Wire.scale.setScalar(1.01);

        const d20Hitbox = new THREE.Mesh(
            new THREE.SphereGeometry(1.4, 8, 8),
            new THREE.MeshBasicMaterial({ visible: false })
        );

        const d20Group = new THREE.Group();
        d20Group.add(d20Mesh);
        d20Group.add(d20Wire);
        d20Group.add(d20Hitbox);
        scene.add(d20Group);

        // --- LAYOUT POSITIONING ---
        function updatePositions() {
            const aspect = window.innerWidth / window.innerHeight;
            const vFOV = THREE.MathUtils.degToRad(camera.fov);
            const height = 2 * Math.tan(vFOV / 2) * camera.position.z;
            const width = height * aspect;

            if (window.innerWidth > 992) {
                d20Group.position.set(width / 4, 0, 0);
            } else {
                d20Group.position.set(0, height / 2 - 1.2, 0);
            }
        }
        updatePositions();

        // --- INTERACTION & RAYCASTING ---
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();
        let isRolling = false;

        function rollDice() {
            if (isRolling) return;
            if (audioCtx.state === 'suspended') audioCtx.resume();
            
            isRolling = true;
            let finalValue = Math.floor(Math.random() * 20) + 1;

            const rollInterval = setInterval(() => {
                let tempVal = Math.floor(Math.random() * 20) + 1;
                d20Material.map = getNumberTexture(tempVal);
                d20Material.needsUpdate = true;
            }, 50);

            gsap.to(d20Group.rotation, {
                x: d20Group.rotation.x + Math.PI * 6 + Math.random(),
                y: d20Group.rotation.y + Math.PI * 6 + Math.random(),
                duration: 1.0,
                ease: 'power2.out',
                onComplete: () => {
                    clearInterval(rollInterval);
                    d20Material.map = getNumberTexture(finalValue);
                    d20Material.needsUpdate = true;
                    isRolling = false;
                    
                    const popup = document.getElementById('dice-result');
                    popup.innerText = finalValue;
                    popup.classList.add('active');
                    
                    playSoftNote(300 + finalValue * 15);
                    setTimeout(() => popup.classList.remove('active'), 1200);
                }
            });
        }

        window.addEventListener('pointerdown', (e) => {
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObjects([d20Mesh, d20Wire, d20Hitbox]);
            if (intersects.length > 0) rollDice();
        });

        const card = document.getElementById('card');
        window.addEventListener('pointermove', (e) => {
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObjects([d20Mesh, d20Wire, d20Hitbox]);
            document.body.style.cursor = intersects.length > 0 ? 'pointer' : 'default';

            const rx = (e.clientY / window.innerHeight - 0.5) * -6;
            const ry = (e.clientX / window.innerWidth - 0.5) * 6;
            card.style.transform = `rotateX(${rx}deg) rotateY(${ry}deg)`;
        });

        // --- ANIMATION LOOP ---
        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);
            const elapsedTime = clock.getElapsedTime();

            if (!isRolling) {
                d20Group.rotation.x = Math.sin(elapsedTime * 0.4) * 0.2;
                d20Group.rotation.y = elapsedTime * 0.25;
            }

            renderer.render(scene, camera);
        }

        animate();

        window.addEventListener('load', () => {
            setTimeout(() => {
                document.querySelectorAll('.stat-bar-fill').forEach(bar => {
                    bar.style.width = bar.getAttribute('data-width');
                });
            }, 200);
        });

        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
            updatePositions();
        });
    </script>
</body>
</html>
