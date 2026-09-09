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
            --glass-bg: rgba(15, 12, 28, 0.75);
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

        .container {
            position: relative;
            z-index: 2;
            max-width: 950px;
            margin: 0 auto;
            padding: 3rem 1.5rem;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            pointer-events: none;
            perspective: 1000px;
        }

        /* 3D Tilt Card */
        .dnd-card {
            pointer-events: auto;
            background: var(--glass-bg);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 2px solid var(--primary-cyan);
            border-radius: 20px;
            padding: 2.8rem;
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

        .badge-container {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            margin-bottom: 1rem;
        }

        .class-badge {
            background: linear-gradient(135deg, var(--dark-purple), #2a1f4d);
            color: var(--primary-cyan);
            padding: 4px 14px;
            border-radius: 20px;
            font-size: 0.85rem;
            letter-spacing: 1px;
            border: 1px solid rgba(135, 232, 203, 0.4);
            box-shadow: 0 0 10px rgba(135, 232, 203, 0.15);
        }

        .brand-title {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 2.4rem;
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
            margin: 0.8rem 0 1.5rem 0;
        }

        h1 {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 2.8rem;
            font-weight: 800;
            color: #ffffff;
            text-shadow: 0 0 15px rgba(135, 232, 203, 0.5), 0 0 25px rgba(172, 169, 222, 0.4);
            margin-bottom: 0.3rem;
        }

        .subtitle {
            font-size: 1.15rem;
            color: var(--primary-purple);
            margin-bottom: 2rem;
            font-weight: 300;
        }

        .info-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 1.5rem;
            margin-bottom: 2rem;
        }

        .info-box {
            background: rgba(18, 14, 36, 0.6);
            border: 1px solid rgba(172, 169, 222, 0.25);
            border-left: 4px solid var(--primary-cyan);
            padding: 1.2rem;
            border-radius: 6px 14px 14px 6px;
            transition: all 0.3s ease;
        }

        .info-box:hover {
            transform: translateY(-4px);
            border-color: var(--primary-cyan);
            box-shadow: 0 8px 20px rgba(135, 232, 203, 0.15);
        }

        .info-box h3 {
            font-size: 1.05rem;
            color: var(--primary-cyan);
            margin-bottom: 0.5rem;
            display: flex;
            align-items: center;
            gap: 8px;
            font-family: 'Cinzel', 'Prompt', serif;
        }

        .info-box p {
            font-size: 0.95rem;
            color: #e0ddf5;
            line-height: 1.6;
        }

        /* RPG Stats Progress Bar */
        .stats-section {
            margin-bottom: 2rem;
        }

        .stat-bar-container {
            margin-bottom: 0.8rem;
        }

        .stat-header {
            display: flex;
            justify-content: space-between;
            font-size: 0.9rem;
            color: var(--primary-purple);
            margin-bottom: 0.3rem;
        }

        .stat-bar-bg {
            width: 100%;
            height: 8px;
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
            transition: width 1.5s cubic-bezier(0.1, 0.5, 0.1, 1);
        }

        .section-title {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 1.4rem;
            color: var(--primary-cyan);
            margin-top: 1rem;
            margin-bottom: 1rem;
            border-bottom: 1px solid var(--border-glow);
            padding-bottom: 0.4rem;
        }

        .skills-container {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
        }

        .skill-tag {
            background: rgba(172, 169, 222, 0.12);
            border: 1px solid var(--primary-purple);
            color: #fff;
            padding: 8px 16px;
            border-radius: 8px;
            font-size: 0.95rem;
            transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            cursor: pointer;
        }

        .skill-tag:hover {
            background: var(--primary-cyan);
            color: var(--bg-color);
            border-color: var(--primary-cyan);
            box-shadow: 0 0 20px var(--primary-cyan);
            transform: translateY(-3px) scale(1.05);
            font-weight: 600;
        }

        /* Overlay Popups */
        .dice-result-popup {
            position: fixed;
            top: 40%;
            left: 50%;
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
            top: 20%;
            left: 50%;
            transform: translate(-50%, -50%) scale(0);
            background: linear-gradient(135deg, #ffd700, #87e8cb);
            color: #0b0914;
            padding: 1rem 3.5rem;
            border-radius: 50px;
            font-family: 'Cinzel', serif;
            font-size: 2.2rem;
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

        @media (max-width: 768px) {
            h1 { font-size: 2.2rem; }
            .brand-title { font-size: 1.8rem; }
            .dnd-card { padding: 1.8rem 1.2rem; }
            .container { padding: 1rem; }
        }
    </style>
</head>
<body>

    <canvas id="webgl-bg"></canvas>

    <button class="audio-toggle" id="audio-toggle">🔊 SFX: ON</button>

    <div class="dice-result-popup" id="dice-result">20</div>
    <div class="nat20-banner" id="nat20-banner">✨ CRITICAL SUCCESS! ✨</div>

    <div class="interactive-hint">
        🎲 คลิกทอยลูกเต๋า D20 | 🔥 คลิกแคมป์ไฟเพื่อเปิด/ดับไฟ
    </div>

    <div class="container">
        <div class="dnd-card" id="card">
            
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
                    <p><strong>มหาวิทยาลัยเทคโนโลยีราชมงคลรัตนโกสินทร์ ศาลายา</strong></p>
                    <p>คณะสถาปัตยกรรมศาสตร์ และการออกแบบ</p>
                    <p>สาขาเกมและอนิเมชั่น (ชั้นปีที่ 4)</p>
                </div>
                
                <div class="info-box">
                    <h3>🎨 สไตล์งานวาด (Art Focus)</h3>
                    <p>เชี่ยวชาญ<strong>การออกแบบตัวละคร (Character Design)</strong> วาดภาพประกอบ (Illustration) งานปั้น 3D และการสร้าง Mod Custom ใน Minecraft</p>
                </div>
            </div>

            <div class="section-title">📊 สเตตัสและความชำนาญ (Character Stats)</div>
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
                    <div class="stat-header"><span>3D Modeling & Assets (Blender / Unity)</span><span>88%</span></div>
                    <div class="stat-bar-bg"><div class="stat-bar-fill" data-width="88%"></div></div>
                </div>
                <div class="stat-bar-container">
                    <div class="stat-header"><span>Minecraft World Building & Figura Modding</span><span>95%</span></div>
                    <div class="stat-bar-bg"><div class="stat-bar-fill" data-width="95%"></div></div>
                </div>
            </div>

            <div class="section-title">⚔️ สกิลความสามารถ (Abilities & Skills)</div>
            <div class="skills-container">
                <div class="skill-tag">🖌️ Digital Art</div>
                <div class="skill-tag">👤 Character Design</div>
                <div class="skill-tag">🖼️ Illustration</div>
                <div class="skill-tag">🏰 Minecraft World Building</div>
                <div class="skill-tag">🦊 Minecraft Figura Modding</div>
                <div class="skill-tag">🕹️ 3D & Game Art</div>
            </div>

        </div>
    </div>

    <script>
        // --- 0. SYNTHESIZED SOUND EFFECTS (Web Audio API) ---
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

        // --- 1. THREE.JS SCENE SETUP ---
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

        // --- 2. LIGHTING ---
        const ambientLight = new THREE.AmbientLight(0x1a152b, 2);
        scene.add(ambientLight);

        const cyanLight = new THREE.PointLight(0x87e8cb, 3, 25);
        cyanLight.position.set(5, 4, 3);
        scene.add(cyanLight);

        const purpleLight = new THREE.PointLight(0xaca9de, 3, 25);
        purpleLight.position.set(-5, -4, 3);
        scene.add(purpleLight);

        // --- 3. D20 DICE & TEXTURE SYSTEM ---
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

        const d20Geo = new THREE.IcosahedronGeometry(0.9, 0);
        const d20Mesh = new THREE.Mesh(d20Geo, d20Material);
        
        const d20Wire = new THREE.Mesh(
            d20Geo,
            new THREE.MeshBasicMaterial({ color: 0x87e8cb, wireframe: true })
        );
        d20Wire.scale.setScalar(1.02);

        const d20Hitbox = new THREE.Mesh(
            new THREE.SphereGeometry(1.2, 8, 8),
            new THREE.MeshBasicMaterial({ visible: false })
        );

        const d20Group = new THREE.Group();
        d20Group.add(d20Mesh);
        d20Group.add(d20Wire);
        d20Group.add(d20Hitbox);
        scene.add(d20Group);

        // --- 4. ADVANCED CAMPFIRE WITH SMOKE & SPARKS ---
        const campfireGroup = new THREE.Group();
        const logMat = new THREE.MeshStandardMaterial({ color: 0x3d2314, roughness: 0.9 });
        for(let i = 0; i < 4; i++) {
            const logGeo = new THREE.CylinderGeometry(0.08, 0.08, 0.75, 8);
            const log = new THREE.Mesh(logGeo, logMat);
            log.rotation.z = Math.PI / 2;
            log.rotation.y = (i * Math.PI) / 4;
            log.position.y = 0.04;
            campfireGroup.add(log);
        }

        const fireLight = new THREE.PointLight(0xff6600, 4.5, 10);
        fireLight.position.set(0, 0.3, 0);
        campfireGroup.add(fireLight);

        // Sparks Particles
        const sparkCount = 40;
        const sparkGeo = new THREE.BufferGeometry();
        const sparkPos = new Float32Array(sparkCount * 3);
        for(let i = 0; i < sparkCount * 3; i += 3) {
            sparkPos[i] = (Math.random() - 0.5) * 0.3;
            sparkPos[i+1] = Math.random() * 0.6;
            sparkPos[i+2] = (Math.random() - 0.5) * 0.3;
        }
        sparkGeo.setAttribute('position', new THREE.BufferAttribute(sparkPos, 3));
        const sparkMat = new THREE.PointsMaterial({
            color: 0xffcc00,
            size: 0.07,
            transparent: true,
            opacity: 0.9
        });
        const sparkParticles = new THREE.Points(sparkGeo, sparkMat);
        campfireGroup.add(sparkParticles);

        const campfireHitbox = new THREE.Mesh(
            new THREE.SphereGeometry(0.8, 8, 8),
            new THREE.MeshBasicMaterial({ visible: false })
        );
        campfireGroup.add(campfireHitbox);

        scene.add(campfireGroup);
        let isFireOn = true;

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
            const speed = 0.1 + Math.random() * 0.15;

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

        // --- 6. FLOATING DUST PARTICLES & RINGS ---
        const bgGroup = new THREE.Group();
        const ringGeo = new THREE.TorusGeometry(2.5, 0.015, 16, 100);
        const ringMat = new THREE.MeshBasicMaterial({ color: 0x87e8cb, wireframe: true });
        const ring = new THREE.Mesh(ringGeo, ringMat);
        ring.rotation.x = Math.PI / 3;
        bgGroup.add(ring);
        scene.add(bgGroup);

        const pCount = 600;
        const pPos = new Float32Array(pCount * 3);
        for(let i = 0; i < pCount * 3; i++) {
            pPos[i] = (Math.random() - 0.5) * 18;
        }
        const pGeo = new THREE.BufferGeometry();
        pGeo.setAttribute('position', new THREE.BufferAttribute(pPos, 3));
        const pMat = new THREE.PointsMaterial({ size: 0.035, color: 0xaca9de, transparent: true, opacity: 0.5 });
        const pMesh = new THREE.Points(pGeo, pMat);
        scene.add(pMesh);

        // --- 7. RESPONSIVE POSITIONING ---
        function updatePositions() {
            const aspect = window.innerWidth / window.innerHeight;
            const vFOV = THREE.MathUtils.degToRad(camera.fov);
            const height = 2 * Math.tan(vFOV / 2) * camera.position.z;
            const width = height * aspect;

            d20Group.position.set(width / 2 - 1.2, height / 2 - 1.2, 0);
            campfireGroup.position.set(-width / 2 + 1.3, -height / 2 + 1.1, 0);
        }
        updatePositions();

        // --- 8. DICE ROLL & INTERACTION LOGIC ---
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
            let counter = 0;

            const rollInterval = setInterval(() => {
                let tempVal = Math.floor(Math.random() * 20) + 1;
                d20Material.map = getOrCreateNumberTexture(tempVal);
                d20Material.needsUpdate = true;
                playRollSound();
                counter++;
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
            const intersectsCampfire = raycaster.intersectObjects(campfireGroup.children);

            if (intersectsD20.length > 0) rollDice();
            if (intersectsCampfire.length > 0) {
                isFireOn = !isFireOn;
                fireLight.intensity = isFireOn ? 4.5 : 0;
                sparkMat.opacity = isFireOn ? 0.9 : 0;
            }
        });

        // Mouse Hover Feedback & Card 3D Tilt Effect
        const card = document.getElementById('card');
        window.addEventListener('pointermove', (e) => {
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;

            // Hover Pointer
            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObjects([d20Mesh, d20Wire, d20Hitbox, ...campfireGroup.children]);
            document.body.style.cursor = intersects.length > 0 ? 'pointer' : 'default';

            // 3D Card Tilt Effect
            const rx = (e.clientY / window.innerHeight - 0.5) * -12;
            const ry = (e.clientX / window.innerWidth - 0.5) * 12;
            card.style.transform = `rotateX(${rx}deg) rotateY(${ry}deg)`;
        });

        // --- 9. ANIMATION LOOP ---
        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);
            const elapsedTime = clock.getElapsedTime();

            // D20 Idle Movement
            if (!isRolling) {
                d20Group.rotation.x = Math.sin(elapsedTime * 0.5) * 0.2;
                d20Group.rotation.y = elapsedTime * 0.4;
            }

            // Campfire Spark Animation
            if (isFireOn) {
                fireLight.intensity = 4 + Math.sin(elapsedTime * 15) * 0.8;
                const pos = sparkParticles.geometry.attributes.position.array;
                for (let i = 1; i < sparkCount * 3; i += 3) {
                    pos[i] += 0.009;
                    if (pos[i] > 0.6) pos[i] = 0;
                }
                sparkParticles.geometry.attributes.position.needsUpdate = true;
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
