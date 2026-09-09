<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Portfolio | ศศิธร เซ้งรักษา (Sasithorn)</title>
    
    <!-- Google Fonts: Prompt & Cinzel for D&D Fantasy Vibe -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800&family=Prompt:wght@300;400;600&display=swap" rel="stylesheet">

    <!-- Three.js CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

    <style>
        :root {
            --bg-color: #0d0b18;
            --primary-cyan: #40e0d0;
            --light-cyan: #7fffd4;
            --primary-purple: #b39ddb;
            --dark-purple: #2a1b4e;
            --text-color: #e0f7fa;
            --glass-bg: rgba(20, 15, 38, 0.65);
            --border-glow: rgba(64, 224, 208, 0.4);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Prompt', sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            overflow-x: hidden;
        }

        /* Canvas 3D Background */
        #webgl-bg {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            z-index: -1;
        }

        /* Overlay Layout */
        .container {
            max-width: 1100px;
            margin: 0 auto;
            padding: 2rem;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
        }

        /* UI Panel (DND Scroll/Card Style) */
        .dnd-card {
            background: var(--glass-bg);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 2px solid var(--primary-cyan);
            border-radius: 16px;
            padding: 2.5rem;
            box-shadow: 0 0 25px rgba(64, 224, 208, 0.2),
                        inset 0 0 15px rgba(179, 157, 219, 0.15);
            position: relative;
            margin-bottom: 2rem;
            animation: fadeIn 1.5s ease-in-out;
        }

        /* Fantasy Corner Decorator */
        .dnd-card::before {
            content: '✦';
            position: absolute;
            top: 10px;
            left: 15px;
            color: var(--primary-purple);
            font-size: 1.2rem;
        }
        .dnd-card::after {
            content: '✦';
            position: absolute;
            bottom: 10px;
            right: 15px;
            color: var(--primary-cyan);
            font-size: 1.2rem;
        }

        /* Header */
        .class-badge {
            display: inline-block;
            background: linear-gradient(45deg, var(--dark-purple), var(--primary-purple));
            color: #fff;
            padding: 4px 16px;
            border-radius: 20px;
            font-size: 0.9rem;
            letter-spacing: 1px;
            margin-bottom: 1rem;
            border: 1px solid var(--light-cyan);
            box-shadow: 0 0 10px rgba(127, 255, 212, 0.3);
        }

        h1 {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 2.8rem;
            color: #fff;
            text-shadow: 0 0 10px var(--primary-cyan), 0 0 20px var(--primary-purple);
            margin-bottom: 0.5rem;
        }

        .subtitle {
            font-size: 1.1rem;
            color: var(--primary-purple);
            margin-bottom: 1.5rem;
            font-weight: 300;
        }

        /* Info Section */
        .info-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 1.5rem;
            margin-top: 1.5rem;
        }

        .info-box {
            background: rgba(42, 27, 78, 0.4);
            border-left: 4px solid var(--primary-cyan);
            padding: 1rem 1.2rem;
            border-radius: 0 8px 8px 0;
        }

        .info-box h3 {
            font-size: 1rem;
            color: var(--light-cyan);
            margin-bottom: 0.3rem;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .info-box p {
            font-size: 0.95rem;
            color: #d1c4e9;
            line-height: 1.5;
        }

        /* Skills / Abilities Section */
        .skills-title {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 1.5rem;
            color: var(--light-cyan);
            margin-top: 2rem;
            margin-bottom: 1rem;
            border-bottom: 1px solid var(--border-glow);
            padding-bottom: 0.5rem;
        }

        .skills-container {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
        }

        .skill-tag {
            background: rgba(179, 157, 219, 0.15);
            border: 1px solid var(--primary-purple);
            color: #fff;
            padding: 8px 16px;
            border-radius: 8px;
            font-size: 0.95rem;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .skill-tag:hover {
            background: var(--primary-cyan);
            color: var(--bg-color);
            box-shadow: 0 0 15px var(--primary-cyan);
            transform: translateY(-3px);
            font-weight: 600;
        }

        /* Animation */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Responsive */
        @media (max-width: 768px) {
            h1 { font-size: 2rem; }
            .dnd-card { padding: 1.5rem; }
        }
    </style>
</head>
<body>

    <!-- 3D Canvas Background -->
    <canvas id="webgl-bg"></canvas>

    <!-- UI Overlay Container -->
    <div class="container">
        <div class="dnd-card">
            <span class="class-badge">LVL 4 • GAME & ANIMATION CREATOR</span>
            <h1>ศศิธร เซ้งรักษา</h1>
            <div class="subtitle">Sasithorn Sengraksa | 3D & Digital Artist Portfolio</div>

            <div class="info-grid">
                <div class="info-box">
                    <h3>📜 การศึกษา (Education)</h3>
                    <p>มหาวิทยาลัยเทคโนโลยีราชมงคลรัตนโกสินทร์ ศาลายา</p>
                    <p>คณะสถาปัตยกรรมศาสตร์และการออกแบบ</p>
                    <p><strong>สาขา:</strong> เกมและอนิเมชั่น (ปี 4)</p>
                </div>
                <div class="info-box">
                    <h3>🔮 สไตล์งาน (Art Style & Interest)</h3>
                    <p>เน้นงานออกแบบฉาก สภาพแวดล้อม 3D Voxel/Blocky โมเดลตัวละคร และงานวาดดิจิทัลแฟนตาซี</p>
                </div>
            </div>

            <div class="skills-title">⚔️ ความถนัดและความสามารถ (Abilities & Skills)</div>
            <div class="skills-container">
                <div class="skill-tag">🎨 วาดรูปดิจิทัล (Digital Art)</div>
                <div class="skill-tag">🏰 สร้างแมพมายคราฟ (Minecraft World Building)</div>
                <div class="skill-tag">🦊 การทำ Figura มายคราฟ (Minecraft Figura Modding)</div>
                <div class="skill-tag">📐 3D Modeling & Design</div>
                <div class="skill-tag">🕹️ Game & Character Design</div>
            </div>
        </div>
    </div>

    <!-- Three.js Script -->
    <script>
        // --- 1. SETUP SCENE, CAMERA, RENDERER ---
        const scene = new THREE.Scene();
        // Add subtle fog for D&D atmosphere
        scene.fog = new THREE.FogExp2(0x0d0b18, 0.03);

        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 5;

        const renderer = new THREE.WebGLRenderer({
            canvas: document.querySelector('#webgl-bg'),
            antialias: true
        });
        renderer.setPixelRatio(window.devicePixelRatio);
        renderer.setSize(window.innerWidth, window.innerHeight);

        // --- 2. LIGHTING (Cyan & Lavender Lights) ---
        const ambientLight = new THREE.AmbientLight(0x2a1b4e, 1.5);
        scene.add(ambientLight);

        // Cyan Point Light
        const cyanLight = new THREE.PointLight(0x40e0d0, 3, 20);
        cyanLight.position.set(3, 2, 2);
        scene.add(cyanLight);

        // Lavender/Purple Point Light
        const purpleLight = new THREE.PointLight(0xb39ddb, 3, 20);
        purpleLight.position.set(-3, -2, 2);
        scene.add(purpleLight);

        // --- 3. 3D OBJECTS (D&D Magic Orb + Runes Ring) ---
        const group = new THREE.Group();

        // Magic Crystal/Orb Center
        const orbGeo = new THREE.IcosahedronGeometry(1.2, 1);
        const orbMat = new THREE.MeshStandardMaterial({
            color: 0x40e0d0,
            wireframe: true,
            emissive: 0x113333,
            roughness: 0.2,
            metalness: 0.8
        });
        const magicOrb = new THREE.Mesh(orbGeo, orbMat);
        group.add(magicOrb);

        // Inner Core Glow
        const coreGeo = new THREE.OctahedronGeometry(0.6, 0);
        const coreMat = new THREE.MeshBasicMaterial({
            color: 0xb39ddb,
            wireframe: false
        });
        const core = new THREE.Mesh(coreGeo, coreMat);
        group.add(core);

        // Outer Magical Ring
        const ringGeo = new THREE.TorusGeometry(2, 0.02, 16, 100);
        const ringMat = new THREE.MeshBasicMaterial({ color: 0x7fffd4, wireframe: true });
        const ring = new THREE.Mesh(ringGeo, ringMat);
        ring.rotation.x = Math.PI / 3;
        group.add(ring);

        scene.add(group);

        // Adjust position relative to screen
        group.position.x = 0;
        group.position.y = 0;

        // --- 4. PARTICLES (Floating Magic Dust) ---
        const particlesCount = 700;
        const posArray = new Float32Array(particlesCount * 3);
        const colorArray = new Float32Array(particlesCount * 3);

        const colorCyan = new THREE.Color(0x40e0d0);
        const colorPurple = new THREE.Color(0xb39ddb);

        for(let i = 0; i < particlesCount * 3; i += 3) {
            // Position
            posArray[i] = (Math.random() - 0.5) * 15;
            posArray[i+1] = (Math.random() - 0.5) * 15;
            posArray[i+2] = (Math.random() - 0.5) * 15;

            // Color mix (Cyan + Purple)
            const mixedColor = Math.random() > 0.5 ? colorCyan : colorPurple;
            colorArray[i] = mixedColor.r;
            colorArray[i+1] = mixedColor.g;
            colorArray[i+2] = mixedColor.b;
        }

        const particlesGeo = new THREE.BufferGeometry();
        particlesGeo.setAttribute('position', new THREE.BufferAttribute(posArray, 3));
        particlesGeo.setAttribute('color', new THREE.BufferAttribute(colorArray, 3));

        const particlesMat = new THREE.PointsMaterial({
            size: 0.04,
            vertexColors: true,
            transparent: true,
            opacity: 0.8
        });

        const particlesMesh = new THREE.Points(particlesGeo, particlesMat);
        scene.add(particlesMesh);

        // --- 5. INTERACTION (Mouse Parallax) ---
        let mouseX = 0;
        let mouseY = 0;

        window.addEventListener('mousemove', (e) => {
            mouseX = (e.clientX / window.innerWidth) - 0.5;
            mouseY = (e.clientY / window.innerHeight) - 0.5;
        });

        // --- 6. ANIMATION LOOP ---
        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);
            const elapsedTime = clock.getElapsedTime();

            // Rotate 3D Objects
            magicOrb.rotation.y = elapsedTime * 0.3;
            magicOrb.rotation.x = elapsedTime * 0.2;
            
            core.rotation.y = -elapsedTime * 0.5;
            core.rotation.z = elapsedTime * 0.3;

            ring.rotation.z = elapsedTime * 0.1;
            ring.rotation.x = Math.PI / 3 + Math.sin(elapsedTime) * 0.1;

            // Rotate Particles Floating
            particlesMesh.rotation.y = elapsedTime * 0.05;
            particlesMesh.rotation.x = -elapsedTime * 0.02;

            // Smooth Mouse Movement Tracking
            group.rotation.y += (mouseX * 0.8 - group.rotation.y) * 0.05;
            group.rotation.x += (-mouseY * 0.8 - group.rotation.x) * 0.05;

            renderer.render(scene, camera);
        }

        animate();

        // --- 7. RESPONSIVE RESIZE ---
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    </script>
</body>
</html>
