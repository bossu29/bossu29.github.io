<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sasithorn S. | Soft D&D Minimal Portfolio</title>
    
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@500;600;700&family=Plus+Jakarta+Sans:wght@300;400;500;600&family=Prompt:wght@300;400;500;600&display=swap" rel="stylesheet">

    <!-- Three.js & GSAP -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>

    <style>
        :root {
            --bg-base: #0f0d18;
            --glass-bg: rgba(25, 21, 38, 0.55);
            --glass-border: rgba(235, 215, 190, 0.18);
            --accent-gold: #f3e5c8;
            --accent-soft-cyan: #b8e3de;
            --accent-rose: #e8b8c8;
            --accent-glow: rgba(243, 229, 200, 0.4);
            --text-main: #f5f3f7;
            --text-sub: #b3acc3;
            --card-radius: 28px;
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
            max-width: 1350px;
            margin: 0 auto;
            padding: 2.5rem 2rem;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: flex-start;
            pointer-events: none;
            perspective: 1200px;
        }

        /* Layout Grid */
        .layout-wrapper {
            display: flex;
            flex-direction: column;
            gap: 1.5rem;
            width: 100%;
            max-width: 620px;
        }

        /* 3D Minimal Soft Glass Card */
        .dnd-card {
            pointer-events: auto;
            background: var(--glass-bg);
            backdrop-filter: blur(24px) saturate(140%);
            -webkit-backdrop-filter: blur(24px) saturate(140%);
            border: 1px solid var(--glass-border);
            border-radius: var(--card-radius);
            padding: 2.5rem 2.2rem;
            width: 100%;
            box-shadow: 0 30px 60px rgba(0, 0, 0, 0.4),
                        inset 0 1px 0 rgba(255, 255, 255, 0.15);
            position: relative;
            transition: transform 0.2s cubic-bezier(0.2, 0, 0.2, 1), box-shadow 0.4s ease;
            transform-style: preserve-3d;
        }

        .dnd-card:hover {
            box-shadow: 0 35px 70px rgba(0, 0, 0, 0.5),
                        0 0 40px rgba(243, 229, 200, 0.12),
                        inset 0 1px 0 rgba(255, 255, 255, 0.25);
        }

        .card-tag {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            background: rgba(243, 229, 200, 0.08);
            border: 1px solid rgba(243, 229, 200, 0.2);
            color: var(--accent-gold);
            padding: 5px 14px;
            border-radius: 30px;
            font-size: 0.75rem;
            letter-spacing: 2px;
            text-transform: uppercase;
            font-family: 'Cormorant Garamond', serif;
            font-weight: 700;
            margin-bottom: 1.2rem;
        }

        .card-tag::before {
            content: '✦';
            font-size: 0.8rem;
            color: var(--accent-gold);
        }

        /* LEFT SIDEBAR / INTERACTIVE WIDGETS */
        .left-interactive-panel {
            pointer-events: auto;
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 1rem;
            width: 100%;
        }

        .widget-box {
            background: var(--glass-bg);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border: 1px solid var(--glass-border);
            border-radius: 20px;
            padding: 1.2rem;
            transition: all 0.3s ease;
        }

        .widget-box:hover {
            border-color: rgba(243, 229, 200, 0.3);
            transform: translateY(-3px);
            box-shadow: 0 10px 25px rgba(0,0,0,0.3);
        }

        .widget-title {
            font-family: 'Cormorant Garamond', serif;
            font-size: 0.95rem;
            color: var(--accent-gold);
            letter-spacing: 1px;
            margin-bottom: 0.8rem;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }

        /* Dice Tray Controls */
        .dice-set {
            display: flex;
            gap: 6px;
            flex-wrap: wrap;
        }

        .dice-btn {
            background: rgba(255, 255, 255, 0.04);
            border: 1px solid rgba(255, 255, 255, 0.1);
            color: var(--text-sub);
            padding: 6px 10px;
            border-radius: 10px;
            font-size: 0.75rem;
            cursor: pointer;
            transition: all 0.2s ease;
            font-family: 'Plus Jakarta Sans', sans-serif;
            flex: 1;
            text-align: center;
        }

        .dice-btn:hover {
            background: rgba(243, 229, 200, 0.15);
            border-color: var(--accent-gold);
            color: var(--accent-gold);
        }

        .dice-display {
            font-family: 'Cormorant Garamond', serif;
            font-size: 1.8rem;
            font-weight: 700;
            color: var(--accent-gold);
            text-align: right;
            line-height: 1;
        }

        /* Spell Cast Buttons */
        .spell-grid {
            display: flex;
            gap: 8px;
        }

        .spell-btn {
            flex: 1;
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 12px;
            padding: 8px;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .spell-btn:hover {
            background: rgba(184, 227, 222, 0.15);
            border-color: var(--accent-soft-cyan);
            transform: scale(1.03);
        }

        .spell-icon {
            font-size: 1.2rem;
            margin-bottom: 2px;
        }

        .spell-name {
            font-size: 0.7rem;
            color: var(--text-sub);
        }

        /* TAB NAVIGATION */
        .tab-nav {
            display: flex;
            gap: 8px;
            margin-bottom: 1.8rem;
            background: rgba(15, 13, 24, 0.4);
            padding: 5px;
            border-radius: 16px;
            border: 1px solid rgba(255, 255, 255, 0.05);
        }

        .tab-btn {
            flex: 1;
            background: transparent;
            border: none;
            color: var(--text-sub);
            padding: 8px 14px;
            border-radius: 10px;
            font-size: 0.85rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);
            font-family: 'Plus Jakarta Sans', 'Prompt', sans-serif;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
        }

        .tab-btn.active {
            background: rgba(243, 229, 200, 0.12);
            color: var(--accent-gold);
            border: 1px solid rgba(243, 229, 200, 0.25);
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
        }

        .tab-btn:hover:not(.active) {
            color: var(--text-main);
            background: rgba(255, 255, 255, 0.04);
        }

        /* TAB CONTENTS */
        .tab-content {
            display: none;
            animation: softFadeIn 0.5s cubic-bezier(0.16, 1, 0.3, 1);
        }

        .tab-content.active {
            display: block;
        }

        .brand-title {
            font-family: 'Cormorant Garamond', serif;
            font-size: 2.8rem;
            font-weight: 600;
            letter-spacing: 1px;
            background: linear-gradient(135deg, #ffffff 30%, var(--accent-gold) 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            line-height: 1.1;
            margin-bottom: 0.2rem;
        }

        .subtitle-th {
            font-size: 1.15rem;
            color: var(--text-main);
            font-weight: 500;
            margin-bottom: 0.2rem;
        }

        .subtitle-en {
            font-size: 0.85rem;
            color: var(--text-sub);
            margin-bottom: 1.5rem;
            font-weight: 300;
            letter-spacing: 0.5px;
        }

        .info-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 0.9rem;
            margin-bottom: 1.5rem;
        }

        .info-box {
            background: rgba(255, 255, 255, 0.025);
            border: 1px solid rgba(255, 255, 255, 0.07);
            padding: 1.1rem;
            border-radius: 16px;
            transition: all 0.3s ease;
        }

        .info-box:hover {
            background: rgba(243, 229, 200, 0.04);
            border-color: rgba(243, 229, 200, 0.2);
            transform: translateY(-2px);
        }

        .info-box h3 {
            font-size: 0.8rem;
            color: var(--accent-gold);
            margin-bottom: 0.4rem;
            text-transform: uppercase;
            letter-spacing: 1.5px;
            font-family: 'Cormorant Garamond', serif;
            font-weight: 700;
        }

        .info-box p {
            font-size: 0.85rem;
            color: var(--text-sub);
            line-height: 1.5;
        }

        .info-box p strong {
            color: var(--text-main);
            font-weight: 500;
        }

        /* Stats Section */
        .section-header {
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-top: 1.5rem;
            margin-bottom: 0.9rem;
        }

        .section-title {
            font-family: 'Cormorant Garamond', serif;
            font-size: 1.15rem;
            color: var(--accent-gold);
            letter-spacing: 1px;
        }

        .stat-bar-container {
            margin-bottom: 0.8rem;
        }

        .stat-header {
            display: flex;
            justify-content: space-between;
            font-size: 0.8rem;
            color: var(--text-sub);
            margin-bottom: 0.3rem;
        }

        .stat-bar-bg {
            width: 100%;
            height: 6px;
            background: rgba(255, 255, 255, 0.06);
            border-radius: 10px;
            overflow: hidden;
        }

        .stat-bar-fill {
            height: 100%;
            background: linear-gradient(90deg, var(--accent-soft-cyan), var(--accent-gold));
            border-radius: 10px;
            width: 0%;
            transition: width 1.2s cubic-bezier(0.16, 1, 0.3, 1);
            box-shadow: 0 0 10px rgba(243, 229, 200, 0.3);
        }

        .skills-container {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            margin-top: 0.8rem;
        }

        .skill-tag {
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.08);
            color: var(--text-main);
            padding: 5px 12px;
            border-radius: 10px;
            font-size: 0.8rem;
            transition: all 0.3s ease;
            cursor: pointer;
        }

        .skill-tag:hover {
            background: rgba(243, 229, 200, 0.12);
            border-color: var(--accent-gold);
            color: var(--accent-gold);
            transform: translateY(-2px);
        }

        /* Contact Items */
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
            background: rgba(255, 255, 255, 0.025);
            border: 1px solid rgba(255, 255, 255, 0.07);
            padding: 1rem 1.2rem;
            border-radius: 16px;
            text-decoration: none;
            color: var(--text-main);
            transition: all 0.3s ease;
        }

        .contact-item:hover {
            background: rgba(243, 229, 200, 0.06);
            border-color: rgba(243, 229, 200, 0.25);
            transform: translateX(6px);
        }

        .contact-icon {
            font-size: 1.1rem;
            background: rgba(243, 229, 200, 0.08);
            width: 40px;
            height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 12px;
            color: var(--accent-gold);
            border: 1px solid rgba(243, 229, 200, 0.15);
        }

        .contact-text h4 {
            font-size: 0.8rem;
            color: var(--accent-gold);
            font-weight: 500;
            margin-bottom: 2px;
            font-family: 'Cormorant Garamond', serif;
            letter-spacing: 1px;
            text-transform: uppercase;
        }

        .contact-text p {
            font-size: 0.85rem;
            color: var(--text-sub);
        }

        /* Popups & Overlay Effects */
        .dice-result-popup {
            position: fixed;
            top: 45%;
            right: 25%;
            transform: translate(50%, -50%) scale(0);
            font-family: 'Cormorant Garamond', serif;
            font-size: 6rem;
            font-weight: 700;
            color: var(--accent-gold);
            text-shadow: 0 0 40px rgba(243, 229, 200, 0.6);
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
            top: 15%;
            left: 50%;
            transform: translate(-50%, -50%) scale(0.8);
            background: rgba(25, 21, 38, 0.85);
            border: 1px solid var(--accent-gold);
            color: var(--accent-gold);
            padding: 0.8rem 2.5rem;
            border-radius: 40px;
            font-family: 'Cormorant Garamond', serif;
            font-size: 1.4rem;
            letter-spacing: 3px;
            box-shadow: 0 10px 40px rgba(0, 0, 0, 0.5), 0 0 30px rgba(243, 229, 200, 0.2);
            backdrop-filter: blur(12px);
            transition: all 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            z-index: 100;
            pointer-events: none;
            opacity: 0;
        }

        .nat20-banner.active {
            transform: translate(-50%, -50%) scale(1);
            opacity: 1;
        }

        .interactive-hint {
            position: fixed;
            bottom: 30px;
            right: 30px;
            background: rgba(25, 21, 38, 0.6);
            border: 1px solid rgba(243, 229, 200, 0.2);
            color: var(--accent-gold);
            padding: 8px 18px;
            border-radius: 30px;
            font-size: 0.8rem;
            backdrop-filter: blur(10px);
            pointer-events: none;
            z-index: 10;
            letter-spacing: 0.5px;
        }

        .audio-toggle {
            position: fixed;
            top: 30px;
            right: 30px;
            z-index: 10;
            background: rgba(25, 21, 38, 0.6);
            border: 1px solid rgba(255, 255, 255, 0.1);
            color: var(--text-sub);
            padding: 8px 16px;
            border-radius: 30px;
            cursor: pointer;
            backdrop-filter: blur(10px);
            font-size: 0.8rem;
            transition: all 0.3s ease;
        }

        .audio-toggle:hover {
            border-color: var(--accent-gold);
            color: var(--accent-gold);
        }

        @keyframes softFadeIn {
            from { opacity: 0; transform: translateY(8px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @media (max-width: 992px) {
            .container {
                justify-content: center;
                padding: 2rem 1rem;
            }
            .layout-wrapper {
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

    <button class="audio-toggle" id="audio-toggle">✨ SOUND: ON</button>

    <div class="dice-result-popup" id="dice-result">20</div>
    <div class="nat20-banner" id="nat20-banner">✦ CRITICAL SUCCESS ✦</div>

    <div class="interactive-hint">
        🎲 คลิกทอยลูกเต๋า D20 ด้านขวา
    </div>

    <div class="container">
        <div class="layout-wrapper">

            <!-- MAIN DND CARD -->
            <div class="dnd-card" id="card">
                
                <div class="card-tag">CHARACTER SHEET</div>

                <!-- Tab Navigation Header -->
                <div class="tab-nav">
                    <button class="tab-btn active" onclick="switchTab('home', this)">🛡️ โปรไฟล์</button>
                    <button class="tab-btn" onclick="switchTab('about', this)">📜 เกี่ยวกับ</button>
                    <button class="tab-btn" onclick="switchTab('contact', this)">📬 ติดต่อ</button>
                </div>

                <!-- TAB 1: HOME PROFILE -->
                <div class="tab-content active" id="tab-home">
                    <div class="brand-title">bossu29</div>
                    <div class="subtitle-th">นางสาว ศศิธร เซ้งรักษา</div>
                    <div class="subtitle-en">Sasithorn Sengraksa | 3D & Digital Character Artist</div>

                    <div class="info-grid">
                        <div class="info-box">
                            <h3>การศึกษา (Education)</h3>
                            <p><strong>มทร.รัตนโกสินทร์ ศาลายา</strong></p>
                            <p>คณะสถาปัตยกรรมศาสตร์และการออกแบบ</p>
                            <p>สาขาเกมและอนิเมชั่น (ปี 4)</p>
                        </div>
                        
                        <div class="info-box">
                            <h3>สายงานที่เชี่ยวชาญ (Specialization)</h3>
                            <p><strong>Character Design & 3D Modeling</strong></p>
                            <p>วาดภาพประกอบ แฟนตาซีไซไฟ และ Minecraft Custom Modding</p>
                        </div>
                    </div>

                    <div class="section-header">
                        <div class="section-title">📊 สเตตัสและความสามารถ (Stats & Mastery)</div>
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
                        <div class="skill-tag">🎨 Digital Art</div>
                        <div class="skill-tag">👤 Character Design</div>
                        <div class="skill-tag">🖼️ Illustration</div>
                        <div class="skill-tag">🏰 Minecraft World Building</div>
                        <div class="skill-tag">🦊 Figura Modding</div>
                        <div class="skill-tag">🕹️ Game Assets</div>
                    </div>
                </div>

                <!-- TAB 2: ABOUT -->
                <div class="tab-content" id="tab-about">
                    <div class="brand-title">ABOUT ME</div>
                    <div class="subtitle-en">Biography & Creative Vision</div>

                    <div class="info-box" style="margin-bottom: 0.9rem;">
                        <h3>ประวัติโดยย่อ (Biography)</h3>
                        <p>สวัสดีค่ะ! ดิฉัน <strong>ศศิธร เซ้งรักษา (bossu29)</strong> เป็นนักศึกษาชั้นปีที่ 4 สาขาเกมและอนิเมชั่น หลงใหลในการดีไซน์ตัวละครแฟนตาซี งานปั้น 3D โมเดลลิ่ง ตลอดจนการสร้างสรรค์ Mod สกิลและอวตารคัสตอมในเกม Minecraft</p>
                    </div>
                    
                    <div class="info-box" style="margin-bottom: 0.9rem;">
                        <h3>เป้าหมาย (Creative Focus)</h3>
                        <p>มุ่งมั่นที่จะถ่ายทอดเรื่องราวและความคิดสร้างสรรค์ผ่าน Character Design คุณภาพสูง เพื่อเติมเต็มจินตนาการและสร้างประสบการณ์ที่ดีในอุตสาหกรรมสื่อสร้างสรรค์และเกม</p>
                    </div>

                    <div class="section-title" style="margin-bottom: 0.5rem;">🛠️ เครื่องมือที่ใช้งาน (Tools & Software)</div>
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
                    <div class="brand-title">GET IN TOUCH</div>
                    <div class="subtitle-en">Contact Channels & Social Media</div>
                    
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
                                <h4>Twitter / X</h4>
                                <p>@bossu29_art</p>
                            </div>
                        </a>
                    </div>
                </div>

            </div>

            <!-- LEFT INTERACTIVE PANEL (NEW ADDITION) -->
            <div class="left-interactive-panel">
                <!-- Mini Dice Roller Widget -->
                <div class="widget-box">
                    <div class="widget-title">
                        <span>🎲 DICE TRAY</span>
                        <span id="sub-dice-result" class="dice-display">--</span>
                    </div>
                    <div class="dice-set">
                        <button class="dice-btn" onclick="rollSubDice(4)">d4</button>
                        <button class="dice-btn" onclick="rollSubDice(6)">d6</button>
                        <button class="dice-btn" onclick="rollSubDice(8)">d8</button>
                        <button class="dice-btn" onclick="rollSubDice(10)">d10</button>
                        <button class="dice-btn" onclick="rollSubDice(12)">d12</button>
                    </div>
                </div>

                <!-- Spell Cast Widget -->
                <div class="widget-box">
                    <div class="widget-title">
                        <span>✨ QUICK CANTRIPS</span>
                    </div>
                    <div class="spell-grid">
                        <div class="spell-btn" onclick="castSpell('Aura of Focus')">
                            <div class="spell-icon">🔮</div>
                            <div class="spell-name">Focus Aura</div>
                        </div>
                        <div class="spell-btn" onclick="castSpell('Inspire')">
                            <div class="spell-icon">📜</div>
                            <div class="spell-name">Inspire</div>
                        </div>
                    </div>
                </div>
            </div>

        </div>
    </div>

    <script>
        // --- TAB SWITCHING SYSTEM ---
        function switchTab(tabName, btnElement) {
            document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
            document.querySelectorAll('.tab-content').forEach(content => content.classList.remove('active'));

            btnElement.classList.add('active');
            document.getElementById(`tab-${tabName}`).classList.add('active');

            if(tabName === 'home') {
                document.querySelectorAll('.stat-bar-fill').forEach(bar => {
                    bar.style.width = '0%';
                    setTimeout(() => bar.style.width = bar.getAttribute('data-width'), 50);
                });
            }
        }

        // --- SUB DICE TRAY & SPELL INTERACTION ---
        function rollSubDice(sides) {
            const display = document.getElementById('sub-dice-result');
            playSoftRollSound();
            let count = 0;
            const interval = setInterval(() => {
                display.innerText = Math.floor(Math.random() * sides) + 1;
                count++;
                if(count > 8) {
                    clearInterval(interval);
                    display.innerText = Math.floor(Math.random() * sides) + 1;
                    playSoftChime();
                }
            }, 50);
        }

        function castSpell(spellName) {
            playSoftChime();
            // Pulse ring light effect
            gsap.to(ring.scale, { x: 1.25, y: 1.25, duration: 0.3, yoyo: true, repeat: 1, ease: "power2.out" });
            gsap.to(goldLight, { intensity: 6, duration: 0.3, yoyo: true, repeat: 1 });
        }

        // --- SOFT MINIMAL SYNTHESIZED SOUNDS ---
        let soundEnabled = true;
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

        document.getElementById('audio-toggle').addEventListener('click', (e) => {
            soundEnabled = !soundEnabled;
            e.target.innerText = soundEnabled ? '✨ SOUND: ON' : '🔇 SOUND: OFF';
        });

        function playSoftRollSound() {
            if (!soundEnabled) return;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'sine';
            osc.frequency.setValueAtTime(300 + Math.random() * 200, audioCtx.currentTime);
            osc.frequency.exponentialRampToValueAtTime(150, audioCtx.currentTime + 0.08);
            gain.gain.setValueAtTime(0.05, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.08);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start();
            osc.stop(audioCtx.currentTime + 0.08);
        }

        function playSoftChime() {
            if (!soundEnabled) return;
            const now = audioCtx.currentTime;
            [523.25, 659.25, 783.99, 1046.50].forEach((freq, i) => {
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(freq, now + i * 0.1);
                gain.gain.setValueAtTime(0.06, now + i * 0.1);
                gain.gain.exponentialRampToValueAtTime(0.001, now + i * 0.1 + 0.5);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now + i * 0.1);
                osc.stop(now + i * 0.1 + 0.5);
            });
        }

        // --- THREE.JS SOFT SCENE SETUP ---
        const scene = new THREE.Scene();
        scene.fog = new THREE.FogExp2(0x0f0d18, 0.025);

        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 5;

        const renderer = new THREE.WebGLRenderer({
            canvas: document.querySelector('#webgl-bg'),
            antialias: true
        });
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.setSize(window.innerWidth, window.innerHeight);

        // --- SOFT LIGHTING ---
        const ambientLight = new THREE.AmbientLight(0xdad5eb, 1.8);
        scene.add(ambientLight);

        const goldLight = new THREE.PointLight(0xf3e5c8, 3, 20);
        goldLight.position.set(4, 4, 4);
        scene.add(goldLight);

        const cyanLight = new THREE.PointLight(0xb8e3de, 2, 20);
        cyanLight.position.set(-4, -3, 2);
        scene.add(cyanLight);

        // --- CRYSTAL D20 DICE ---
        const d20Textures = {};
        function getNumberTexture(number) {
            if (d20Textures[number]) return d20Textures[number];

            const canvas = document.createElement('canvas');
            canvas.width = 256;
            canvas.height = 256;
            const ctx = canvas.getContext('2d');

            ctx.fillStyle = '#1e1a2e';
            ctx.fillRect(0, 0, 256, 256);

            ctx.strokeStyle = '#f3e5c8';
            ctx.lineWidth = 12;
            ctx.strokeRect(12, 12, 232, 232);

            ctx.fillStyle = '#f3e5c8';
            ctx.font = '600 110px Cormorant Garamond, serif';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText(number.toString(), 128, 130);

            const texture = new THREE.CanvasTexture(canvas);
            d20Textures[number] = texture;
            return texture;
        }

        const d20Geo = new THREE.IcosahedronGeometry(1.2, 0);
        
        const d20Material = new THREE.MeshPhysicalMaterial({
            map: getNumberTexture(20),
            roughness: 0.1,
            metalness: 0.1,
            transmission: 0.6,
            opacity: 0.9,
            transparent: true,
            ior: 1.5,
            reflectivity: 0.5
        });

        const d20Mesh = new THREE.Mesh(d20Geo, d20Material);
        
        const d20Wire = new THREE.Mesh(
            d20Geo,
            new THREE.MeshBasicMaterial({ color: 0xf3e5c8, wireframe: true, transparent: true, opacity: 0.4 })
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

        // --- SOFT MAGIC DUST PARTICLES ---
        const pCount = 500;
        const pPos = new Float32Array(pCount * 3);
        for(let i = 0; i < pCount * 3; i++) {
            pPos[i] = (Math.random() - 0.5) * 18;
        }
        const pGeo = new THREE.BufferGeometry();
        pGeo.setAttribute('position', new THREE.BufferAttribute(pPos, 3));
        const pMat = new THREE.PointsMaterial({
            size: 0.03,
            color: 0xf3e5c8,
            transparent: true,
            opacity: 0.4
        });
        const pMesh = new THREE.Points(pGeo, pMat);
        scene.add(pMesh);

        // --- ELEGANT RING ---
        const ringGeo = new THREE.TorusGeometry(2.8, 0.008, 16, 100);
        const ringMat = new THREE.MeshBasicMaterial({ color: 0xf3e5c8, transparent: true, opacity: 0.3 });
        const ring = new THREE.Mesh(ringGeo, ringMat);
        ring.rotation.x = Math.PI / 2.5;
        scene.add(ring);

        // --- POSITIONING ---
        function updatePositions() {
            const aspect = window.innerWidth / window.innerHeight;
            const vFOV = THREE.MathUtils.degToRad(camera.fov);
            const height = 2 * Math.tan(vFOV / 2) * camera.position.z;
            const width = height * aspect;

            if (window.innerWidth > 992) {
                d20Group.position.set(width / 4 + 0.3, 0, 0);
                ring.position.copy(d20Group.position);
            } else {
                d20Group.position.set(width / 2 - 1.2, height / 2 - 1.2, 0);
                ring.position.copy(d20Group.position);
            }
        }
        updatePositions();

        // --- ROLL INTERACTION ---
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();
        let isRolling = false;

        function showResultPopup(value) {
            const popup = document.getElementById('dice-result');
            const banner = document.getElementById('nat20-banner');
            popup.innerText = value;
            
            if(value === 20) {
                banner.classList.add('active');
                playSoftChime();
                setTimeout(() => banner.classList.remove('active'), 3000);
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
                d20Material.map = getNumberTexture(tempVal);
                d20Material.needsUpdate = true;
                playSoftRollSound();
            }, 70);

            gsap.to(d20Group.rotation, {
                x: d20Group.rotation.x + Math.PI * 4 + Math.random(),
                y: d20Group.rotation.y + Math.PI * 4 + Math.random(),
                duration: 1.2,
                ease: 'power2.out',
                onComplete: () => {
                    clearInterval(rollInterval);
                    d20Material.map = getNumberTexture(finalValue);
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

        // Mouse Hover & Soft Tilt Card
        const card = document.getElementById('card');
        window.addEventListener('pointermove', (e) => {
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObjects([d20Mesh, d20Wire, d20Hitbox]);
            document.body.style.cursor = intersects.length > 0 ? 'pointer' : 'default';

            const rx = (e.clientY / window.innerHeight - 0.5) * -5;
            const ry = (e.clientX / window.innerWidth - 0.5) * 5;
            card.style.transform = `rotateX(${rx}deg) rotateY(${ry}deg)`;
        });

        // --- ANIMATION LOOP ---
        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);
            const elapsedTime = clock.getElapsedTime();

            if (!isRolling) {
                d20Group.rotation.x = Math.sin(elapsedTime * 0.4) * 0.15;
                d20Group.rotation.y = elapsedTime * 0.25;
            }

            ring.rotation.z = elapsedTime * 0.08;
            pMesh.rotation.y = elapsedTime * 0.02;

            renderer.render(scene, camera);
        }

        animate();

        // Stat Bar Animation
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
