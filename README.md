<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Portfolio | ศศิธร เซ้งรักษา (Sasithorn)</title>
    
    <!-- Google Fonts: Prompt & Cinzel for D&D Fantasy Vibe -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800&family=Prompt:wght@300;400;500;600&display=swap" rel="stylesheet">

    <!-- Three.js CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

    <style>
        :root {
            --bg-color: #0b0813;
            --primary-cyan: #40e0d0;
            --light-cyan: #7fffd4;
            --primary-purple: #b39ddb;
            --dark-purple: #23143c;
            --text-color: #e0f7fa;
            --glass-bg: rgba(18, 13, 34, 0.72);
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
            min-height: 100vh;
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

        /* Container Layout */
        .container {
            max-width: 1000px;
            margin: 0 auto;
            padding: 2.5rem 1.5rem;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
        }

        /* UI Panel (DND Character Sheet / Quest Card Style) */
        .dnd-card {
            background: var(--glass-bg);
            backdrop-filter: blur(14px);
            -webkit-backdrop-filter: blur(14px);
            border: 2px solid var(--primary-cyan);
            border-radius: 18px;
            padding: 2.5rem;
            box-shadow: 0 0 30px rgba(64, 224, 208, 0.25),
                        inset 0 0 20px rgba(179, 157, 219, 0.15);
            position: relative;
            animation: fadeIn 1.2s ease-out;
        }

        /* D&D Decorative Corners */
        .dnd-card::before {
            content: '✦  CLASS SHEET  ✦';
            position: absolute;
            top: -12px;
            left: 50%;
            transform: translateX(-50%);
            background: var(--dark-purple);
            color: var(--light-cyan);
            border: 1px solid var(--primary-cyan);
            padding: 2px 16px;
            border-radius: 12px;
            font-size: 0.75rem;
            letter-spacing: 2px;
            font-family: 'Cinzel', serif;
        }

        /* Header Details */
        .badge-container {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            margin-bottom: 0.8rem;
        }

        .class-badge {
            background: linear-gradient(135deg, var(--dark-purple), #3a2266);
            color: var(--light-cyan);
            padding: 4px 14px;
            border-radius: 20px;
            font-size: 0.85rem;
            letter-spacing: 1px;
            border: 1px solid rgba(127, 255, 212, 0.4);
            box-shadow: 0 0 10px rgba(127, 255, 212, 0.2);
        }

        h1 {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 2.8rem;
            font-weight: 800;
            color: #ffffff;
            text-shadow: 0 0 12px var(--primary-cyan), 0 0 24px var(--primary-purple);
            margin-bottom: 0.3rem;
        }

        .subtitle {
            font-size: 1.1rem;
            color: var(--primary-purple);
            margin-bottom: 1.8rem;
            font-weight: 300;
        }

        /* Info Grid Layout */
        .info-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 1.5rem;
            margin-bottom: 1.8rem;
        }

        .info-box {
            background: rgba(35, 20, 60, 0.45);
            border: 1px solid rgba(179, 157, 219, 0.25);
            border-left: 4px solid var(--primary-cyan);
            padding: 1.2rem;
            border-radius: 4px 12px 12px 4px;
            transition: transform 0.3s ease, border-color 0.3s ease;
        }

        .info-box:hover {
            transform: translateY(-2px);
            border-color: var(--light-cyan);
        }

        .info-box h3 {
            font-size: 1.05rem;
            color: var(--light-cyan);
            margin-bottom: 0.5rem;
            display: flex;
            align-items: center;
            gap: 8px;
            font-family: 'Cinzel', 'Prompt', serif;
        }

        .info-box p {
            font-size: 0.95rem;
            color: #d1c4e9;
            line-height: 1.6;
        }

        /* Skills & Specialty Section */
        .section-title {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 1.4rem;
            color: var(--light-cyan);
            margin-top: 1rem;
            margin-bottom: 1rem;
            border-bottom: 1px solid var(--border-glow);
            padding-bottom: 0.4rem;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .skills-container {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
        }

        .skill-tag {
            background: rgba(179, 157, 219, 0.12);
            border: 1px solid var(--primary-purple);
            color: #fff;
            padding: 8px 16px;
            border-radius: 8px;
            font-size: 0.95rem;
            transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            cursor: default;
        }

        .skill-tag:hover {
            background: var(--primary-cyan);
            color: var(--bg-color);
            border-color: var(--light-cyan);
            box-shadow: 0 0 15px var(--primary-cyan);
            transform: translateY(-3px) scale(1.03);
            font-weight: 600;
        }

        /* Animations */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Responsive */
        @media (max-width: 768px) {
            h1 { font-size: 2.1rem; }
            .dnd-card { padding: 1.8rem 1.2rem; }
            .container { padding: 1rem; }
        }
    </style>
</head>
<body>

    <!-- 3D Canvas Background -->
    <canvas id="webgl-bg"></canvas>

    <!-- UI Overlay Container -->
    <div class="container">
        <div class="dnd-card">
            
            <div class="badge-container">
                <span class="class-badge">LVL 4 ARTIST & DESIGNER</span>
                <span class="class-badge">GAME & ANIMATION</span>
            </div>

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
                    <h3>🎨 สไตล์งานวาด (Art Style Focus)</h3>
                    <p>เน้น<strong>การออกแบบตัวละคร (Character Design)</strong> และการวาดภาพประกอบชิ้นงานประเภทต่างๆ (Illustrations) ผสมผสานความถนัดด้าน 3D & Modding</p>
                </div>
            </div>

            <div class="section-title">⚔️ ความถนัดและความสามารถ (Abilities & Skills)</div>
            <div class="skills-container">
                <div class="skill-tag">🖌️ วาดรูปดิจิทัล (Digital Art)</div>
                <div class="skill-tag">👤 การออกแบบตัวละคร (Character Design)</div>
                <div class="skill-tag">🖼️ วาดภาพประกอบ (Illustration)</div>
                <div class="skill-tag">🏰 สร้างแมพมายคราฟ (Minecraft World Building)</div>
                <div class="skill-tag">🦊 การทำ Figura มายคราฟ (Minecraft Figura Modding)</div>
                <div class="skill-tag">🕹️ 3D & Game Art</div>
            </div>

        </div>
    </div>

    <!-- Three.js Script -->
    <script>
        // --- 1. SETUP SCENE, CAMERA, RENDERER ---
        const scene = new THREE.Scene();
        scene.fog = new THREE.FogExp2(0x0b0813, 0.035);

        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 5;

        const renderer = new THREE.WebGLRenderer({
            canvas: document.querySelector('#webgl-bg'),
            antialias: true
        });
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.setSize(window.innerWidth, window.innerHeight);

        // --- 2. LIGHTING (Cyan & Purple Magic Lights) ---
        const ambientLight = new THREE.AmbientLight(0x23143c, 1.8);
        scene.add(ambientLight);

        const cyanLight = new THREE.PointLight(0x40e0d0, 3, 25);
        cyanLight.position.set(4, 3, 2);
        scene.add(cyanLight);

        const purpleLight = new THREE.PointLight(0xb39ddb, 3, 25);
        purpleLight.position.set(-4, -3, 2);
        scene.add(purpleLight);

        // --- 3. 3D OBJECTS (Magical D&D Crystal Core & Rings) ---
        const mainGroup = new THREE.Group();

        // Outer Icosahedron Sphere (Cyan Wireframe)
        const orbGeo = new THREE.IcosahedronGeometry(1.3, 1);
        const orbMat = new THREE.MeshStandardMaterial({
            color: 0x40e0d0,
            wireframe: true,
            emissive: 0x0a2b2a,
            roughness: 0.2,
            metalness: 0.8
        });
        const magicOrb = new THREE.Mesh(orbGeo, orbMat);
        mainGroup.add(magicOrb);

        // Inner Crystal Core (Purple Solid Octahedron)
        const coreGeo = new THREE.OctahedronGeometry(0.6, 0);
        const coreMat = new THREE.MeshBasicMaterial({
            color: 0xb39ddb,
            wireframe: false
        });
        const core = new THREE.Mesh(coreGeo, coreMat);
        mainGroup.add(core);

        // Magical Ring 1
        const ringGeo1 = new THREE.TorusGeometry(2.1, 0.015, 16, 100);
        const ringMat1 = new THREE.MeshBasicMaterial({ color: 0x7fffd4, wireframe: true });
        const ring1 = new THREE.Mesh(ringGeo1, ringMat1);
        ring1.rotation.x = Math.PI / 3;
        mainGroup.add(ring1);

        // Magical Ring 2
        const ringGeo2 = new THREE.TorusGeometry(2.5, 0.01, 16, 100);
        const ringMat2 = new THREE.MeshBasicMaterial({ color: 0xb39ddb, wireframe: true });
        const ring2 = new THREE.Mesh(ringGeo2, ringMat2);
        ring2.rotation.y = Math.PI / 4;
        mainGroup.add(ring2);

        scene.add(mainGroup);

        // --- 4. PARTICLES (Floating Magic Mana Dust) ---
        const particlesCount = 800;
        const posArray = new Float32Array(particlesCount * 3);
        const colorArray = new Float32Array(particlesCount * 3);

        const colorCyan = new THREE.Color(0x40e0d0);
        const colorPurple = new THREE.Color(0xb39ddb);

        for(let i = 0; i < particlesCount * 3; i += 3) {
            posArray[i] = (Math.random() - 0.5) * 16;
            posArray[i+1] = (Math.random() - 0.5) * 16;
            posArray[i+2] = (Math.random() - 0.5) * 16;

            const mixedColor = Math.random() > 0.4 ? colorCyan : colorPurple;
            colorArray[i] = mixedColor.r;
            colorArray[i+1] = mixedColor.g;
            colorArray[i+2] = mixedColor.b;
        }

        const particlesGeo = new THREE.BufferGeometry();
        particlesGeo.setAttribute('position', new THREE.BufferAttribute(posArray, 3));
        particlesGeo.setAttribute('color', new THREE.BufferAttribute(colorArray, 3));

        const particlesMat = new THREE.PointsMaterial({
            size: 0.035,
            vertexColors: true,
            transparent: true,
            opacity: 0.75
        });

        const particlesMesh = new THREE.Points(particlesGeo, particlesMat);
        scene.add(particlesMesh);

        // --- 5. INTERACTION (Mouse Parallax Effect) ---
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

            // Rotations
            magicOrb.rotation.y = elapsedTime * 0.25;
            magicOrb.rotation.x = elapsedTime * 0.15;
            
            core.rotation.y = -elapsedTime * 0.4;
            core.rotation.z = elapsedTime * 0.2;

            ring1.rotation.z = elapsedTime * 0.1;
            ring1.rotation.x = Math.PI / 3 + Math.sin(elapsedTime * 0.8) * 0.15;

            ring2.rotation.y = elapsedTime * 0.12;
            ring2.rotation.z = Math.sin(elapsedTime * 0.5) * 0.2;

            // Particles floating rotation
            particlesMesh.rotation.y = elapsedTime * 0.03;
            particlesMesh.rotation.x = -elapsedTime * 0.015;

            // Smooth Mouse Movement Tracking (Parallax)
            mainGroup.rotation.y += (mouseX * 0.6 - mainGroup.rotation.y) * 0.05;
            mainGroup.rotation.x += (-mouseY * 0.6 - mainGroup.rotation.x) * 0.05;

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
