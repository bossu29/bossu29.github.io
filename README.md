<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Portfolio | ศศิธร เซ้งรักษา</title>
    
    <!-- Google Fonts: Prompt & Cinzel -->
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

        #webgl-bg {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            z-index: 1; /* วาง Canvas ไว้ให้อยู่ใน Layer ที่รับ Raycast ได้ */
        }

        .container {
            position: relative;
            z-index: 2; /* วางข้อความเหนือ Canvas */
            max-width: 900px;
            margin: 0 auto;
            padding: 2.5rem 1.5rem;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            pointer-events: none; /* เพื่อให้พื้นที่ว่างคลิกผ่านไปยัง 3D Canvas ได้ */
        }

        .dnd-card {
            pointer-events: auto; /* ให้คลิกหรือคลุมดำเลือกข้อความภายในการ์ดได้ตามปกติ */
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

        .info-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
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

        /* Hints UI */
        .interactive-hint {
            position: fixed;
            bottom: 25px;
            right: 25px;
            background: rgba(26, 21, 43, 0.85);
            border: 1px solid var(--primary-cyan);
            color: var(--primary-cyan);
            padding: 8px 16px;
            border-radius: 20px;
            font-size: 0.85rem;
            box-shadow: 0 0 15px rgba(135, 232, 203, 0.2);
            backdrop-filter: blur(8px);
            pointer-events: none;
            z-index: 10;
        }

        .nat20-banner {
            position: fixed;
            top: 20%;
            left: 50%;
            transform: translate(-50%, -50%) scale(0);
            background: linear-gradient(135deg, #87e8cb, #aca9de);
            color: #0b0914;
            padding: 1rem 3rem;
            border-radius: 50px;
            font-family: 'Cinzel', serif;
            font-size: 2rem;
            font-weight: 800;
            box-shadow: 0 0 40px #87e8cb;
            transition: transform 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            z-index: 100;
            pointer-events: none;
        }

        .nat20-banner.active {
            transform: translate(-50%, -50%) scale(1);
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @media (max-width: 768px) {
            h1 { font-size: 2.1rem; }
            .brand-title { font-size: 1.8rem; }
            .dnd-card { padding: 1.8rem 1.2rem; }
            .container { padding: 1rem; }
        }
    </style>
</head>
<body>

    <canvas id="webgl-bg"></canvas>

    <div class="nat20-banner" id="nat20-banner">✨ NATURAL 20! ✨</div>

    <div class="interactive-hint">
        🎲 คลิก D20 (มุมขวาบน) เพื่อสุ่มดวง | 🔥 คลิกแคมป์ไฟ (มุมซ้ายล่าง) เพื่อจุด/ดับไฟ
    </div>

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
        // --- 1. SETUP SCENE & CAMERA ---
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

        const cyanLight = new THREE.PointLight(0x87e8cb, 2.5, 25);
        cyanLight.position.set(4, 3, 2);
        scene.add(cyanLight);

        const purpleLight = new THREE.PointLight(0xaca9de, 2.5, 25);
        purpleLight.position.set(-4, -3, 2);
        scene.add(purpleLight);

        // --- 3. D20 WITH NUMBERS & TEXTURES ---
        function createNumberTexture(number) {
            const canvas = document.createElement('canvas');
            canvas.width = 256;
            canvas.height = 256;
            const ctx = canvas.getContext('2d');

            ctx.fillStyle = '#aca9de';
            ctx.fillRect(0, 0, 256, 256);

            ctx.strokeStyle = '#87e8cb';
            ctx.lineWidth = 14;
            ctx.strokeRect(8, 8, 240, 240);

            ctx.fillStyle = '#0b0914';
            ctx.font = 'Bold 120px Cinzel, sans-serif';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText(number.toString(), 128, 128);

            return new THREE.CanvasTexture(canvas);
        }

        const d20Materials = [];
        for (let i = 1; i <= 20; i++) {
            d20Materials.push(new THREE.MeshStandardMaterial({
                map: createNumberTexture(i),
                roughness: 0.3,
                metalness: 0.4,
                flatShading: true
            }));
        }

        const d20Geo = new THREE.IcosahedronGeometry(0.85, 0);
        const d20Mesh = new THREE.Mesh(d20Geo, d20Materials);
        
        const d20Wire = new THREE.Mesh(
            d20Geo,
            new THREE.MeshBasicMaterial({ color: 0x87e8cb, wireframe: true })
        );
        d20Wire.scale.setScalar(1.02);

        const d20Group = new THREE.Group();
        d20Group.add(d20Mesh);
        d20Group.add(d20Wire);
        scene.add(d20Group);

        // --- 4. INTERACTIVE CAMPFIRE ---
        const campfireGroup = new THREE.Group();

        const logMat = new THREE.MeshStandardMaterial({ color: 0x4a2e18, roughness: 0.9 });
        for(let i = 0; i < 3; i++) {
            const logGeo = new THREE.CylinderGeometry(0.08, 0.08, 0.7, 8);
            const log = new THREE.Mesh(logGeo, logMat);
            log.rotation.z = Math.PI / 2;
            log.rotation.y = (i * Math.PI) / 3;
            log.position.y = 0.04;
            campfireGroup.add(log);
        }

        // Hitbox ซ่อนไว้เพื่อช่วยให้คลิกแคมป์ไฟได้ง่ายขึ้น
        const campfireHitboxGeo = new THREE.SphereGeometry(0.6, 8, 8);
        const campfireHitboxMat = new THREE.MeshBasicMaterial({ visible: false });
        const campfireHitbox = new THREE.Mesh(campfireHitboxGeo, campfireHitboxMat);
        campfireGroup.add(campfireHitbox);

        const fireLight = new THREE.PointLight(0xff6600, 4, 8);
        fireLight.position.set(0, 0.3, 0);
        campfireGroup.add(fireLight);

        const fireParticlesCount = 60;
        const fireGeo = new THREE.BufferGeometry();
        const firePositions = new Float32Array(fireParticlesCount * 3);

        for(let i = 0; i < fireParticlesCount * 3; i += 3) {
            firePositions[i] = (Math.random() - 0.5) * 0.25;
            firePositions[i+1] = Math.random() * 0.45;
            firePositions[i+2] = (Math.random() - 0.5) * 0.25;
        }

        fireGeo.setAttribute('position', new THREE.BufferAttribute(firePositions, 3));
        const fireMat = new THREE.PointsMaterial({
            color: 0xffaa00,
            size: 0.09,
            transparent: true,
            opacity: 0.8
        });
        const fireParticles = new THREE.Points(fireGeo, fireMat);
        campfireGroup.add(fireParticles);

        scene.add(campfireGroup);
        let isFireOn = true;

        // --- 5. NAT 20 SHOOTING STARS ---
        const starsCount = 180;
        const starsGeo = new THREE.BufferGeometry();
        const starsPos = new Float32Array(starsCount * 3);
        const starsVel = [];

        for(let i = 0; i < starsCount * 3; i += 3) {
            starsPos[i] = (Math.random() - 0.5) * 20;
            starsPos[i+1] = Math.random() * 10 + 5;
            starsPos[i+2] = (Math.random() - 0.5) * 10;
            starsVel.push({
                x: -0.12 - Math.random() * 0.1,
                y: -0.25 - Math.random() * 0.2
            });
        }

        starsGeo.setAttribute('position', new THREE.BufferAttribute(starsPos, 3));
        const starsMat = new THREE.PointsMaterial({
            color: 0x87e8cb,
            size: 0.1,
            transparent: true,
            opacity: 0
        });
        const shootingStars = new THREE.Points(starsGeo, starsMat);
        scene.add(shootingStars);

        let isNat20Active = false;
        let nat20Timer = 0;

        function triggerNat20Effect() {
            isNat20Active = true;
            nat20Timer = 0;
            starsMat.opacity = 1;
            const banner = document.getElementById('nat20-banner');
            banner.classList.add('active');

            setTimeout(() => {
                banner.classList.remove('active');
            }, 3000);
        }

        // --- 6. BACKGROUND RINGS & DUST ---
        const bgGroup = new THREE.Group();
        const ringGeo = new THREE.TorusGeometry(2.3, 0.012, 16, 100);
        const ringMat = new THREE.MeshBasicMaterial({ color: 0x87e8cb, wireframe: true });
        const ring = new THREE.Mesh(ringGeo, ringMat);
        ring.rotation.x = Math.PI / 3;
        bgGroup.add(ring);
        scene.add(bgGroup);

        const particlesCount = 500;
        const pArray = new Float32Array(particlesCount * 3);
        for(let i = 0; i < particlesCount * 3; i++) {
            pArray[i] = (Math.random() - 0.5) * 16;
        }
        const pGeo = new THREE.BufferGeometry();
        pGeo.setAttribute('position', new THREE.BufferAttribute(pArray, 3));
        const pMat = new THREE.PointsMaterial({ size: 0.03, color: 0xaca9de, transparent: true, opacity: 0.6 });
        const pMesh = new THREE.Points(pGeo, pMat);
        scene.add(pMesh);

        // --- 7. POSITIONING & RESPONSIVE ---
        function updatePositions() {
            const aspect = window.innerWidth / window.innerHeight;
            const vFOV = THREE.MathUtils.degToRad(camera.fov);
            const height = 2 * Math.tan(vFOV / 2) * camera.position.z;
            const width = height * aspect;

            // วาง D20 มุมขวาบน
            d20Group.position.set(width / 2 - 1.1, height / 2 - 1.1, 0);

            // วาง Campfire มุมซ้ายล่าง
            campfireGroup.position.set(-width / 2 + 1.2, -height / 2 + 1.0, 0);
        }
        updatePositions();

        // --- 8. RAYCASTING & CLICK INTERACTION (ปรับปรุงให้คลิกง่ายขึ้น) ---
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();
        let isRolling = false;
        let rollTargetRotation = { x: 0, y: 0, z: 0 };

        window.addEventListener('pointerdown', (e) => {
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            
            // ตรวจจับการคลิกที่ D20 และ Campfire
            const intersectsD20 = raycaster.intersectObjects([d20Mesh, d20Wire]);
            const intersectsCampfire = raycaster.intersectObjects(campfireGroup.children);

            // 1. ถ้าคลิกที่ D20
            if (intersectsD20.length > 0) {
                if (!isRolling) {
                    isRolling = true;
                    const rolledValue = Math.floor(Math.random() * 20) + 1;

                    rollTargetRotation = {
                        x: d20Group.rotation.x + Math.PI * 6 + Math.random() * Math.PI,
                        y: d20Group.rotation.y + Math.PI * 6 + Math.random() * Math.PI,
                        z: d20Group.rotation.z + Math.PI * 2
                    };

                    setTimeout(() => {
                        isRolling = false;
                        if (rolledValue === 20) {
                            triggerNat20Effect();
                        }
                    }, 1000);
                }
            }

            // 2. ถ้าคลิกที่ Campfire
            if (intersectsCampfire.length > 0) {
                isFireOn = !isFireOn;
                fireLight.intensity = isFireOn ? 4 : 0;
                fireMat.opacity = isFireOn ? 0.8 : 0;
            }
        });

        // Mouse Cursor Hover Feedback
        window.addEventListener('pointermove', (e) => {
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObjects([d20Mesh, d20Wire, ...campfireGroup.children]);

            if (intersects.length > 0) {
                document.body.style.cursor = 'pointer';
            } else {
                document.body.style.cursor = 'default';
            }
        });

        // --- 9. ANIMATION LOOP ---
        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);
            const elapsedTime = clock.getElapsedTime();

            // D20 Rotation
            if (isRolling) {
                d20Group.rotation.x += (rollTargetRotation.x - d20Group.rotation.x) * 0.12;
                d20Group.rotation.y += (rollTargetRotation.y - d20Group.rotation.y) * 0.12;
                d20Group.rotation.z += (rollTargetRotation.z - d20Group.rotation.z) * 0.12;
            } else {
                d20Group.rotation.x = Math.sin(elapsedTime * 0.5) * 0.3;
                d20Group.rotation.y = elapsedTime * 0.4;
            }

            // Campfire Animation
            if (isFireOn) {
                fireLight.intensity = 3.5 + Math.sin(elapsedTime * 12) * 0.8;
                const positions = fireParticles.geometry.attributes.position.array;
                for (let i = 1; i < fireParticlesCount * 3; i += 3) {
                    positions[i] += 0.008;
                    if (positions[i] > 0.45) positions[i] = 0;
                }
                fireParticles.geometry.attributes.position.needsUpdate = true;
            }

            // Nat 20 Shooting Stars Animation
            if (isNat20Active) {
                nat20Timer += 0.016;
                const pos = shootingStars.geometry.attributes.position.array;
                for (let i = 0; i < starsCount; i++) {
                    pos[i * 3] += starsVel[i].x;
                    pos[i * 3 + 1] += starsVel[i].y;

                    if (pos[i * 3 + 1] < -6) {
                        pos[i * 3 + 1] = Math.random() * 6 + 5;
                        pos[i * 3] = (Math.random() - 0.5) * 20;
                    }
                }
                shootingStars.geometry.attributes.position.needsUpdate = true;

                if (nat20Timer > 4) {
                    starsMat.opacity -= 0.01;
                    if (starsMat.opacity <= 0) {
                        isNat20Active = false;
                    }
                }
            }

            // Ambient background animation
            ring.rotation.z = elapsedTime * 0.1;

            renderer.render(scene, camera);
        }

        animate();

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
