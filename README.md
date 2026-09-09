<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Portfolio | ศศิธร เซ้งรักษา</title>
    
    <!-- Google Fonts: Prompt & Cinzel for D&D Vibe -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800&family=Prompt:wght@300;400;500;600&display=swap" rel="stylesheet">

    <!-- Three.js CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

    <style>
        :root {
            --bg-color: #0b0914;
            --primary-cyan: #87e8cb;    /* ฟ้าน้ำทะเล */
            --primary-purple: #aca9de;  /* ม่วงอ่อน */
            --dark-purple: #1a152b;
            --text-color: #f0f4f8;
            --glass-bg: rgba(18, 15, 30, 0.75);
            --border-glow: rgba(135, 232, 203, 0.4);
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

        /* UI Panel (DND Character Sheet Card Style) */
        .dnd-card {
            background: var(--glass-bg);
            backdrop-filter: blur(14px);
            -webkit-backdrop-filter: blur(14px);
            border: 2px solid var(--primary-cyan);
            border-radius: 18px;
            padding: 2.5rem;
            box-shadow: 0 0 30px rgba(135, 232, 203, 0.25),
                        inset 0 0 20px rgba(172, 169, 222, 0.15);
            position: relative;
            animation: fadeIn 1.2s ease-out;
        }

        /* D&D Decorative Tag */
        .dnd-card::before {
            content: '✦ D&D CHARACTER SHEET ✦';
            position: absolute;
            top: -12px;
            left: 50%;
            transform: translateX(-50%);
            background: var(--dark-purple);
            color: var(--primary-cyan);
            border: 1px solid var(--primary-cyan);
            padding: 2px 16px;
            border-radius: 12px;
            font-size: 0.75rem;
            letter-spacing: 2px;
            font-family: 'Cinzel', serif;
        }

        /* Header Badges */
        .badge-container {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            margin-bottom: 0.8rem;
        }

        .class-badge {
            background: linear-gradient(135deg, var(--dark-purple), #2d2645);
            color: var(--primary-cyan);
            padding: 4px 14px;
            border-radius: 20px;
            font-size: 0.85rem;
            letter-spacing: 1px;
            border: 1px solid rgba(135, 232, 203, 0.4);
            box-shadow: 0 0 10px rgba(135, 232, 203, 0.15);
        }

        /* Name & Brand Styling */
        .brand-title {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 2.2rem;
            font-weight: 800;
            letter-spacing: 2px;
            background: linear-gradient(135deg, #ffffff 20%, var(--primary-cyan) 60%, var(--primary-purple) 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            filter: drop-shadow(0 0 8px rgba(135, 232, 203, 0.5));
            margin-bottom: 0.2rem;
        }

        .divider-line {
            border: 0;
            height: 1.5px;
            background: linear-gradient(90deg, var(--primary-cyan), var(--primary-purple), transparent);
            margin: 0.8rem 0 1.5rem 0;
        }

        h1 {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 2.6rem;
            font-weight: 800;
            color: #ffffff;
            text-shadow: 0 0 12px rgba(135, 232, 203, 0.4), 0 0 20px rgba(172, 169, 222, 0.3);
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
            background: rgba(26, 21, 43, 0.5);
            border: 1px solid rgba(172, 169, 222, 0.25);
            border-left: 4px solid var(--primary-cyan);
            padding: 1.2rem;
            border-radius: 4px 12px 12px 4px;
            transition: transform 0.3s ease, border-color 0.3s ease;
        }

        .info-box:hover {
            transform: translateY(-2px);
            border-color: var(--primary-cyan);
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

        /* Skills Section */
        .section-title {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 1.4rem;
            color: var(--primary-cyan);
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
            background: rgba(172, 169, 222, 0.12);
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
            border-color: var(--primary-cyan);
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
            .brand-title { font-size: 1.8rem; }
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
        scene.fog = new THREE.FogExp2(0x0b0914, 0.035);

        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 5;

        const renderer = new THREE.WebGLRenderer({
            canvas: document.querySelector('#webgl-bg'),
            antialias: true
        });
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.setSize(window.innerWidth, window.innerHeight);

        // --- 2. LIGHTING ---
        const ambientLight = new THREE.AmbientLight(0x1a152b, 1.8);
        scene.add(ambientLight);

        const cyanLight = new THREE.PointLight(0x87e8cb, 3, 25);
        cyanLight.position.set(4, 3, 2);
        scene.add(cyanLight);

        const purpleLight = new THREE.PointLight(0xaca9de, 3, 25);
        purpleLight.position.set(-4, -3, 2);
        scene.add(purpleLight);

        // --- 3. 3D D20 DICE AT CORNER ---
        const d20Group = new THREE.Group();

        const d20Geo = new THREE.IcosahedronGeometry(0.7, 0);
        const d20Mat = new THREE.MeshStandardMaterial({
            color: 0xaca9de,
            roughness: 0.3,
            metalness: 0.6,
            flatShading: true
        });
        const d20Mesh = new THREE.Mesh(d20Geo, d20Mat);
        d20Group.add(d20Mesh);

        const d20WireMat = new THREE.MeshBasicMaterial({
            color: 0x87e8cb,
            wireframe: true
        });
        const d20WireMesh = new THREE.Mesh(d20Geo, d20WireMat);
        d20WireMesh.scale.setScalar(1.02);
        d20Group.add(d20WireMesh);

        scene.add(d20Group);

        // --- 4. 3D CENTRAL RINGS & CORE ---
        const mainGroup = new THREE.Group();

        const coreGeo = new THREE.OctahedronGeometry(0.5, 0);
        const coreMat = new THREE.MeshBasicMaterial({ color: 0x87e8cb });
        const core = new THREE.Mesh(coreGeo, coreMat);
        mainGroup.add(core);

        const ringGeo1 = new THREE.TorusGeometry(2.2, 0.015, 16, 100);
        const ringMat1 = new THREE.MeshBasicMaterial({ color: 0x87e8cb, wireframe: true });
        const ring1 = new THREE.Mesh(ringGeo1, ringMat1);
        ring1.rotation.x = Math.PI / 3;
        mainGroup.add(ring1);

        const ringGeo2 = new THREE.TorusGeometry(2.6, 0.01, 16, 100);
        const ringMat2 = new THREE.MeshBasicMaterial({ color: 0xaca9de, wireframe: true });
        const ring2 = new THREE.Mesh(ringGeo2, ringMat2);
        ring2.rotation.y = Math.PI / 4;
        mainGroup.add(ring2);

        scene.add(mainGroup);

        // --- 5. PARTICLES ---
        const particlesCount = 750;
        const posArray = new Float32Array(particlesCount * 3);
        const colorArray = new Float32Array(particlesCount * 3);

        const colorCyan = new THREE.Color(0x87e8cb);
        const colorPurple = new THREE.Color(0xaca9de);

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

        // --- 6. POSITIONING FOR D20 ---
        function updateD20Position() {
            const aspect = window.innerWidth / window.innerHeight;
            const vFOV = THREE.MathUtils.degToRad(camera.fov);
            const height = 2 * Math.tan(vFOV / 2) * camera.position.z;
            const width = height * aspect;

            d20Group.position.set(width / 2 - 0.9, height / 2 - 0.9, 0);
        }
        updateD20Position();

        // --- 7. INTERACTION ---
        let mouseX = 0;
        let mouseY = 0;

        window.addEventListener('mousemove', (e) => {
            mouseX = (e.clientX / window.innerWidth) - 0.5;
            mouseY = (e.clientY / window.innerHeight) - 0.5;
        });

        // --- 8. ANIMATION LOOP ---
        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);
            const elapsedTime = clock.getElapsedTime();

            d20Group.rotation.x = elapsedTime * 0.4;
            d20Group.rotation.y = elapsedTime * 0.6;

            core.rotation.y = -elapsedTime * 0.4;
            core.rotation.z = elapsedTime * 0.2;

            ring1.rotation.z = elapsedTime * 0.1;
            ring1.rotation.x = Math.PI / 3 + Math.sin(elapsedTime * 0.8) * 0.15;

            ring2.rotation.y = elapsedTime * 0.12;
            ring2.rotation.z = Math.sin(elapsedTime * 0.5) * 0.2;

            particlesMesh.rotation.y = elapsedTime * 0.03;
            particlesMesh.rotation.x = -elapsedTime * 0.015;

            mainGroup.rotation.y += (mouseX * 0.6 - mainGroup.rotation.y) * 0.05;
            mainGroup.rotation.x += (-mouseY * 0.6 - mainGroup.rotation.x) * 0.05;
            
            d20Group.rotation.z = mouseX * 0.5;

            renderer.render(scene, camera);
        }

        animate();

        // --- 9. RESPONSIVE RESIZE ---
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
            updateD20Position();
        });
    </script>
</body>
</html>
