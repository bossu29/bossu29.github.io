<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Portfolio | นางสาว ศศิธร เซ้งรักษา (bossu29)</title>
    
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
            --glass-bg: rgba(15, 12, 28, 0.88);
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
            width: 100%;
            max-width: 1300px;
            margin: 0 auto;
            padding: 2.5rem 2rem;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: flex-start;
            pointer-events: none;
            perspective: 1500px;
        }

        /* 3D Book / Card Container */
        .dnd-card {
            pointer-events: auto;
            background: var(--glass-bg);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 2px solid var(--primary-cyan);
            border-radius: 20px;
            padding: 2.5rem;
            width: 100%;
            max-width: 620px;
            box-shadow: 0 0 40px rgba(135, 232, 203, 0.2),
                        inset 0 0 30px rgba(172, 169, 222, 0.15);
            position: relative;
            transition: transform 0.1s ease-out, box-shadow 0.3s ease;
            transform-style: preserve-3d;
        }

        .dnd-card:hover {
            box-shadow: 0 0 60px rgba(135, 232, 203, 0.35),
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
            z-index: 10;
        }

        /* TAB NAVIGATION */
        .tab-nav {
            display: flex;
            gap: 10px;
            margin-bottom: 1.5rem;
            border-bottom: 1px solid rgba(135, 232, 203, 0.3);
            padding-bottom: 0.8rem;
            position: relative;
            z-index: 10;
        }

        .tab-btn {
            background: rgba(172, 169, 222, 0.1);
            border: 1px solid var(--primary-purple);
            color: var(--primary-purple);
            padding: 8px 18px;
            border-radius: 12px;
            font-size: 0.9rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s ease;
            font-family: 'Cinzel', 'Prompt', serif;
        }

        .tab-btn.active, .tab-btn:hover {
            background: var(--primary-cyan);
            color: var(--bg-color);
            border-color: var(--primary-cyan);
            box-shadow: 0 0 15px var(--primary-cyan);
            font-weight: 600;
        }

        /* 3D BOOK FLIP SYSTEM */
        .pages-wrapper {
            position: relative;
            min-height: 480px;
            perspective: 1200px;
            transform-style: preserve-3d;
        }

        .tab-content {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            opacity: 0;
            visibility: hidden;
            transform-origin: left center;
            transform: rotateY(-90deg);
            backface-visibility: hidden;
            transition: none;
            z-index: 1;
        }

        .tab-content.active {
            opacity: 1;
            visibility: visible;
            transform: rotateY(0deg);
            z-index: 5;
        }

        /* Page Shadow Overlay during Flip */
        .page-shadow {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, rgba(0,0,0,0.6) 0%, rgba(0,0,0,0) 100%);
            pointer-events: none;
            opacity: 0;
            z-index: 6;
            border-radius: 12px;
            transition: opacity 0.3s ease;
        }

        .badge-container {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
            margin-bottom: 0.8rem;
        }

        .class-badge {
            background: linear-gradient(135deg, var(--dark-purple), #2a1f4d);
            color: var(--primary-cyan);
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 0.8rem;
            letter-spacing: 1px;
            border: 1px solid rgba(135, 232, 203, 0.4);
            box-shadow: 0 0 10px rgba(135, 232, 203, 0.15);
        }

        .brand-title {
            font-family: 'Cinzel', 'Prompt', serif;
            font-size: 2.2rem;
            font-weight: 900;
            letter-spacing: 2px;
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
            text-shadow: 0 0 15px rgba(135, 232, 203, 0.5), 0 0 25px rgba(172, 169, 222, 0.4);
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
            border-radius: 6px 12px 12px 6px;
            transition: all 0.3s ease;
        }

        .info-box:hover {
            transform: translateY(-3px);
            border-color: var(--primary-cyan);
            box-shadow: 0 5px 15px rgba(135, 232, 203, 0.15);
        }

        .info-box h3 {
            font-size: 1rem;
            color: var(--primary-cyan);
            margin-bottom: 0.4rem;
            display: flex;
            align-items: center;
            gap: 8px;
            font-family: 'Cinzel', 'Prompt', serif;
        }

        .info-box p {
            font-size: 0.9rem;
            color: #e0ddf5;
            line-height: 1.5;
        }

        /* Stats Bar */
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
            font-size: 0.85rem;
            transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            cursor: pointer;
        }

        .skill-tag:hover {
            background: var(--primary-cyan);
            color: var(--bg-color);
            border-color: var(--primary-cyan);
            box-shadow: 0 0 15px var(--primary-cyan);
            transform: translateY(-2px) scale(1.03);
            font-weight: 600;
        }

        /* Contact Items Style */
        .contact-list {
            display: flex;
            flex-direction: column;
            gap: 1rem;
            margin-top: 1rem;
        }

        .contact-item {
            display: flex;
            align-items: center;
            gap: 15px;
            background: rgba(18, 14, 36, 0.6);
            border: 1px solid rgba(135, 232, 203, 0.3);
            padding: 1rem 1.2rem;
            border-radius: 12px;
            text-decoration: none;
            color: var(--text-color);
            transition: all 0.3s ease;
        }

        .contact-item:hover {
            background: rgba(135, 232, 203, 0.15);
            border-color: var(--primary-cyan);
            transform: translateX(8px);
            box-shadow: 0 0 15px rgba(135, 232, 203, 0.2);
        }

        .contact-icon {
            font-size: 1.5rem;
            background: var(--dark-purple);
            width: 45px;
            height: 45px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            border: 1px solid var(--primary-cyan);
            color: var(--primary-cyan);
        }

        .contact-text h4 {
            font-size: 0.95rem;
            color: var(--primary-cyan);
            margin-bottom: 2px;
        }

        .contact-text p {
            font-size: 0.85rem;
            color: var(--primary-purple);
        }

        /* Overlay Popups & Hints */
        .dice-result-popup {
            position: fixed;
            top: 45%;
            right: 25%;
            transform: translate(50%, -50%) scale(0);
            font-family: 'Cinzel', serif;
            font-size: 5.5rem;
            font-weight: 900;
            color: #fff;
            text-shadow: 0 0 30px var(--primary-cyan);
            pointer-events: none;
            z-index: 99;
            transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275), opacity 0.4s;
            opacity: 0;
        }

        .dice-result-popup.active {
            transform: translate(50%, -50%) scale(1);
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

        /* Sparkle Magic Particles on Flip */
        .sparkle {
            position: absolute;
            width: 6px;
            height: 6px;
            background: var(--primary-cyan);
            border-radius: 50%;
            box-shadow: 0 0 10px var(--primary-cyan);
            pointer-events: none;
            z-index: 20;
        }

        @media (max-width: 992px) {
            .container {
                justify-content: center;
                padding: 2rem 1rem;
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

    <button class="audio-toggle" id="audio-toggle">🔊 SFX: ON</button>

    <div class="dice-result-popup" id="dice-result">20</div>
    <div class="nat20-banner" id="nat20-banner">✨ CRITICAL SUCCESS! ✨</div>

    <div class="interactive-hint">
        🎲 คลิกทอยลูกเต๋า D20 (ด้านขวา) เพื่อเสี่ยงดวง!
    </div>

    <div class="container">
        <div class="dnd-card" id="card">
            
            <!-- Tab Navigation Header -->
            <div class="tab-nav">
                <button class="tab-btn active" onclick="switchTab('home', this)">🛡️ โปรไฟล์ (Profile)</button>
                <button class="tab-btn" onclick="switchTab('about', this)">📜 เกี่ยวกับ (About)</button>
                <button class="tab-btn" onclick="switchTab('contact', this)">📬 ติดต่อ (Contact)</button>
            </div>

            <!-- PAGES WRAPPER (3D BOOK EFFECT) -->
            <div class="pages-wrapper">
                <div class="page-shadow" id="page-shadow"></div>

                <!-- TAB 1: HOME PROFILE -->
                <div class="tab-content active" id="tab-home">
                    <div class="badge-container">
                        <span class="class-badge">LVL 4 ARTIST & DESIGNER</span>
                        <span class="class-badge">GAME & ANIMATION</span>
                        <span class="class-badge">MINECRAFT MODDER</span>
                    </div>

                    <div class="brand-title">bossu29</div>
                    <hr class="divider-line">

                    <h1>นางสาว ศศิธร เซ้งรักษา</h1>
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
                            <p>เชี่ยวชาญ<strong>การออกแบบตัวละคร (Character Design)</strong> วาดภาพประกอบ (Illustration) งานปั้น 3D และการทำ Mod Custom ใน Minecraft</p>
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

                <!-- TAB 2: ABOUT -->
                <div class="tab-content" id="tab-about">
                    <div class="brand-title">ABOUT ME</div>
                    <hr class="divider-line">
                    <div class="info-box" style="margin-bottom: 1.2rem;">
                        <h3>✨ เกี่ยวกับฉัน (Biography)</h3>
                        <p>สวัสดีค่ะ! ดิฉัน <strong>นางสาว ศศิธร เซ้งรักษา (bossu29)</strong> เป็นนักศึกษาชั้นปีที่ 4 สาขาเกมและอนิเมชั่น ผู้มีความหลงใหลในงานดิจิทัลอาร์ต การออกแบบตัวละครแฟนตาซีไซไฟ การทำ 3D โมเดลลิ่ง และการปรับแต่งโมดูลเกม อย่างเช่น Minecraft Figura Modding</p>
                    </div>
                    <div class="info-box" style="margin-bottom: 1.2rem;">
                        <h3>🎯 เป้าหมายการทำงาน (Work Goal)</h3>
                        <p>มุ่งมั่นที่จะสร้างสรรค์ผลงาน Character Design และ Game Asset ระดับคุณภาพ เพื่อเติมเต็มจินตนาการและสร้างประสบการณ์ที่น่าจดจำในอุตสาหกรรมสื่อสร้างสรรค์และเกม</p>
                    </div>
                    <div class="section-title">🛠️ เครื่องมือที่เชี่ยวชาญ (Software & Tools)</div>
                    <div class="skills-container">
                        <div class="skill-tag">Blender 3D</div>
                        <div class="skill-tag">Unity Engine</div>
                        <div class="skill-tag">Clip Studio Paint</div>
                        <div class="skill-tag">Adobe Photoshop</div>
                        <div class="skill-tag">Blockbench</div>
                    </div>
                </div>

                <!-- TAB 3: CONTACT -->
                <div class="tab-content" id="tab-contact">
                    <div class="brand-title">GET IN TOUCH</div>
                    <hr class="divider-line">
                    <p style="color: var(--primary-purple); font-size: 0.95rem;">สนใจร่วมงาน สอบถามข้อมูล หรือดูผลงานเพิ่มเติม สามารถติดต่อดิฉันได้ผ่านช่องทางด้านล่างนี้เลยค่ะ:</p>
                    
                    <div class="contact-list">
                        <a href="mailto:sengraksa2005@gmail.com" class="contact-item">
                            <div class="contact-icon">📧</div>
                            <div class="contact-text">
                                <h4>Email Address</h4>
                                <p>sengraksa2005@gmail.com</p>
                            </div>
                        </a>

                        <div class="contact-item">
                            <div class="contact-icon">💬</div>
                            <div class="contact-text">
                                <h4>Discord</h4>
                                <p>bossu_u</p>
                            </div>
                        </div>

                        <a href="#" class="contact-item">
                            <div class="contact-icon">🌐</div>
                            <div class="contact-text">
                                <h4>Social Media (Twitter/X)</h4>
                                <p>@bossu29_art</p>
                            </div>
                        </a>
                    </div>
                </div>

            </div>

        </div>
    </div>

    <script>
        // --- SYNTHESIZED SOUND EFFECTS ---
        let soundEnabled = true;
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

        document.getElementById('audio-toggle').addEventListener('click', (e) => {
            soundEnabled = !soundEnabled;
            e.target.innerText = soundEnabled ? '🔊 SFX: ON' : '🔇 SFX: OFF';
        });

        // Book Page Flip Sound Effect
        function playPageFlipSound() {
            if (!soundEnabled) return;
            if (audioCtx.state === 'suspended') audioCtx.resume();
            
            const bufferSize = audioCtx.sampleRate * 0.15;
            const buffer = audioCtx.createBuffer(1, bufferSize, audioCtx.sampleRate);
            const data = buffer.getChannelData(0);
            for (let i = 0; i < bufferSize; i++) {
                data[i] = (Math.random() * 2 - 1) * Math.exp(-i / (bufferSize * 0.3));
            }

            const noise = audioCtx.createBufferSource();
            noise.buffer = buffer;

            const filter = audioCtx.createBiquadFilter();
            filter.type = 'lowpass';
            filter.frequency.setValueAtTime(800, audioCtx.currentTime);
            filter.frequency.exponentialRampToValueAtTime(150, audioCtx.currentTime + 0.15);

            const gain = audioCtx.createGain();
            gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);

            noise.connect(filter);
            filter.connect(gain);
            gain.connect(audioCtx.destination);

            noise.start();
        }

        function playRollSound() {
            if (!soundEnabled) return;
            if (audioCtx.state === 'suspended') audioCtx.resume();
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
            if (audioCtx.state === 'suspended') audioCtx.resume();
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

        // --- SPARKLE PARTICLES FOR PAGE FLIP ---
        function createPageSparkles() {
            const card = document.getElementById('card');
            const rect = card.getBoundingClientRect();
            
            for(let i = 0; i < 15; i++) {
                const sparkle = document.createElement('div');
                sparkle.className = 'sparkle';
                
                const startX = rect.left + Math.random() * 50;
                const startY = rect.top + Math.random() * rect.height;
                
                sparkle.style.left = `${startX}px`;
                sparkle.style.top = `${startY}px`;
                document.body.appendChild(sparkle);

                gsap.to(sparkle, {
                    x: (Math.random() - 0.2) * 120,
                    y: (Math.random() - 0.5) * 100,
                    opacity: 0,
                    scale: 0.2,
                    duration: 0.6 + Math.random() * 0.4,
                    ease: "power2.out",
                    onComplete: () => sparkle.remove()
                });
            }
        }

        // --- GSAP 3D BOOK PAGE FLIP ANIMATION ---
        let isAnimatingTab = false;

        function switchTab(tabName, btnElement) {
            if (isAnimatingTab) return;
            const currentActive = document.querySelector('.tab-content.active');
            const targetContent = document.getElementById(`tab-${tabName}`);

            if (currentActive === targetContent) return;

            isAnimatingTab = true;
            playPageFlipSound();
            createPageSparkles();

            // Update Tab Active State
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
            btnElement.classList.add('active');

            const shadow = document.getElementById('page-shadow');

            // Determine flip direction
            const allTabs = Array.from(document.querySelectorAll('.tab-content'));
            const currentIndex = allTabs.indexOf(currentActive);
            const targetIndex = allTabs.indexOf(targetContent);
            const isNext = targetIndex > currentIndex;

            // Prepare GSAP Timeline for 3D Book Flip Effect
            const tl = gsap.timeline({
                onComplete: () => {
                    currentActive.classList.remove('active');
                    targetContent.classList.add('active');
                    gsap.set([currentActive, targetContent], { clearProps: "all" });
                    isAnimatingTab = false;

                    // Trigger Stat Bars inside Home
                    if(tabName === 'home') {
                        document.querySelectorAll('.stat-bar-fill').forEach(bar => {
                            bar.style.width = '0%';
                            setTimeout(() => bar.style.width = bar.getAttribute('data-width'), 50);
                        });
                    }
                }
            });

            // Shadow animation during flip
            tl.to(shadow, { opacity: 0.8, duration: 0.2, ease: "power1.in" }, 0)
              .to(shadow, { opacity: 0, duration: 0.25, ease: "power1.out" }, 0.25);

            // Current Page Flip Out
            tl.to(currentActive, {
                rotateY: isNext ? -90 : 90,
                scale: 0.96,
                filter: "brightness(0.6)",
                duration: 0.25,
                ease: "power2.in"
            }, 0);

            // Target Page Flip In
            gsap.set(targetContent, {
                display: 'block',
                visibility: 'visible',
                opacity: 0,
                rotateY: isNext ? 90 : -90,
                scale: 0.96,
                filter: "brightness(0.5)"
            });

            tl.to(targetContent, {
                opacity: 1,
                rotateY: 0,
                scale: 1,
                filter: "brightness(1)",
                duration: 0.3,
                ease: "power2.out"
            }, 0.2);
        }

        // --- THREE.JS SCENE SETUP ---
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

        // --- LIGHTING ---
        const ambientLight = new THREE.AmbientLight(0x1a152b, 2.2);
        scene.add(ambientLight);

        const cyanLight = new THREE.PointLight(0x87e8cb, 3.5, 25);
        cyanLight.position.set(5, 4, 3);
        scene.add(cyanLight);

        const purpleLight = new THREE.PointLight(0xaca9de, 3.5, 25);
        purpleLight.position.set(-5, -4, 3);
        scene.add(purpleLight);

        // --- D20 DICE CREATION ---
        const d20Geometry = new THREE.IcosahedronGeometry(1.2, 0);
        const d20Material = new THREE.MeshStandardMaterial({
            color: 0x120e24,
            roughness: 0.2,
            metalness: 0.8,
            wireframe: false,
            flatShading: true
        });

        const diceGroup = new THREE.Group();
        const diceMesh = new THREE.Mesh(d20Geometry, d20Material);
        diceGroup.add(diceMesh);

        // Add glowing wireframe edges to D20
        const wireframeGeo = new THREE.WireframeGeometry(d20Geometry);
        const wireframeMat = new THREE.LineBasicMaterial({ color: 0x87e8cb, linewidth: 2 });
        const wireframe = new THREE.LineSegments(wireframeGeo, wireframeMat);
        diceGroup.add(wireframe);

        // Position D20 to the right side
        diceGroup.position.set(2.8, 0, 0);
        scene.add(diceGroup);

        // --- BACKGROUND PARTICLES ---
        const particleCount = 200;
        const particleGeo = new THREE.BufferGeometry();
        const particlePos = new Float32Array(particleCount * 3);

        for (let i = 0; i < particleCount * 3; i += 3) {
            particlePos[i] = (Math.random() - 0.5) * 20;
            particlePos[i + 1] = (Math.random() - 0.5) * 20;
            particlePos[i + 2] = (Math.random() - 0.5) * 15;
        }

        particleGeo.setAttribute('position', new THREE.BufferAttribute(particlePos, 3));
        const particleMat = new THREE.PointsMaterial({
            size: 0.05,
            color: 0x87e8cb,
            transparent: true,
            opacity: 0.6
        });
        const particles = new THREE.Points(particleGeo, particleMat);
        scene.add(particles);

        // --- D20 INTERACTION & ROLLING MECHANIC ---
        let isRolling = false;
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();

        window.addEventListener('pointerdown', (e) => {
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObject(diceMesh);

            if (intersects.length > 0 && !isRolling) {
                rollDice();
            }
        });

        function rollDice() {
            isRolling = true;
            const dicePopup = document.getElementById('dice-result');
            const banner = document.getElementById('nat20-banner');
            
            dicePopup.classList.remove('active');
            banner.classList.remove('active');

            const result = Math.floor(Math.random() * 20) + 1;
            
            // Roll sound effects during rotation
            let interval = setInterval(() => {
                playRollSound();
            }, 100);

            gsap.to(diceGroup.rotation, {
                x: diceGroup.rotation.x + Math.PI * 8 + Math.random() * 2,
                y: diceGroup.rotation.y + Math.PI * 8 + Math.random() * 2,
                z: diceGroup.rotation.z + Math.PI * 4,
                duration: 1.5,
                ease: "power4.out",
                onComplete: () => {
                    clearInterval(interval);
                    isRolling = false;
                    dicePopup.innerText = result;
                    dicePopup.classList.add('active');

                    if (result === 20) {
                        playWinSound();
                        banner.classList.add('active');
                    }

                    setTimeout(() => {
                        dicePopup.classList.remove('active');
                        if (result === 20) banner.classList.remove('active');
                    }, 2500);
                }
            });

            gsap.to(diceGroup.position, {
                y: 0.8,
                duration: 0.3,
                yoyo: true,
                repeat: 1,
                ease: "power2.out"
            });
        }

        // --- MOUSE PARALLAX & CARD TILT ---
        let targetX = 0, targetY = 0;
        window.addEventListener('mousemove', (e) => {
            targetX = (e.clientX / window.innerWidth - 0.5) * 0.5;
            targetY = (e.clientY / window.innerHeight - 0.5) * 0.5;

            // Subtle 3D Card Parallax Tilt
            const card = document.getElementById('card');
            const rotX = (e.clientY / window.innerHeight - 0.5) * -12;
            const rotY = (e.clientX / window.innerWidth - 0.5) * 12;
            card.style.transform = `rotateX(${rotX}deg) rotateY(${rotY}deg)`;
        });

        // Responsive layout adjustment for Three.js
        function handleResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);

            if (window.innerWidth <= 992) {
                diceGroup.position.set(0, -2.5, 0);
            } else {
                diceGroup.position.set(2.8, 0, 0);
            }
        }
        window.addEventListener('resize', handleResize);
        handleResize();

        // Initial Stat Bar trigger on load
        window.addEventListener('DOMContentLoaded', () => {
            setTimeout(() => {
                document.querySelectorAll('.stat-bar-fill').forEach(bar => {
                    bar.style.width = bar.getAttribute('data-width');
                });
            }, 300);
        });

        // --- ANIMATION LOOP ---
        const clock = new THREE.Clock();
        function animate() {
            requestAnimationFrame(animate);
            const elapsedTime = clock.getElapsedTime();

            // Floating idle movement for D20
            if (!isRolling) {
                diceGroup.rotation.x += 0.005;
                diceGroup.rotation.y += 0.008;
                diceGroup.position.y += Math.sin(elapsedTime * 2) * 0.002;
            }

            // Slowly rotate particles
            particles.rotation.y = elapsedTime * 0.03;

            // Smooth Camera Parallax
            camera.position.x += (targetX - camera.position.x) * 0.05;
            camera.position.y += (-targetY - camera.position.y) * 0.05;
            camera.lookAt(scene.position);

            renderer.render(scene, camera);
        }
        animate();
    </script>
</body>
</html>
