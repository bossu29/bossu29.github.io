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

        // --- D20 DICE SYSTEM ---
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

        // --- FIREWORKS BURST FOR NAT 20 ---
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

        // --- BACKGROUND PARTICLES & RINGS ---
        const bgGroup = new THREE.Group();
        const ringGeo = new THREE.TorusGeometry(3.0, 0.015, 16, 100);
        const ringMat = new THREE.MeshBasicMaterial({ color: 0x87e8cb, wireframe: true });
        const ring = new THREE.Mesh(ringGeo, ringMat);
        ring.rotation.x = Math.PI / 3;
        bgGroup.add(ring);
        scene.add(bgGroup);

        const pCount = 700;
        const pPos = new Float32Array(pCount * 3);
        for(let i = 0; i < pCount * 3; i++) {
            pPos[i] = (Math.random() - 0.5) * 20;
        }
        const pGeo = new THREE.BufferGeometry();
        pGeo.setAttribute('position', new THREE.BufferAttribute(pPos, 3));
        const pMat = new THREE.PointsMaterial({ size: 0.035, color: 0xaca9de, transparent: true, opacity: 0.5 });
        const pMesh = new THREE.Points(pGeo, pMat);
        scene.add(pMesh);

        // --- POSITIONING ---
        function updatePositions() {
            const aspect = window.innerWidth / window.innerHeight;
            const vFOV = THREE.MathUtils.degToRad(camera.fov);
            const height = 2 * Math.tan(vFOV / 2) * camera.position.z;
            const width = height * aspect;

            if (window.innerWidth > 992) {
                d20Group.position.set(width / 4 + 0.5, 0, 0);
            } else {
                d20Group.position.set(width / 2 - 1.2, height / 2 - 1.2, 0);
            }
        }
        updatePositions();

        // --- DICE ROLL & INTERACTION ---
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

            const rx = (e.clientY / window.innerHeight - 0.5) * -10;
            const ry = (e.clientX / window.innerWidth - 0.5) * 10;
            card.style.transform = `rotateX(${rx}deg) rotateY(${ry}deg)`;
        });

        // --- ANIMATION LOOP ---
        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);
            const elapsedTime = clock.getElapsedTime();

            if (!isRolling) {
                d20Group.rotation.x = Math.sin(elapsedTime * 0.5) * 0.2;
                d20Group.rotation.y = elapsedTime * 0.4;
            }

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

            ring.rotation.z = elapsedTime * 0.1;
            pMesh.rotation.y = elapsedTime * 0.03;

            renderer.render(scene, camera);
        }

        animate();

        // Animate Stat Bars on page load
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
