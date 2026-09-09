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
            z-index: 1;
        }

        .container {
            position: relative;
            z-index: 2;
            max-width: 900px;
            margin: 0 auto;
            padding: 2.5rem 1.5rem;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            pointer-events: none;
        }

        .dnd-card {
            pointer-events: auto;
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

        /* Roll Output Popup */
        .roll-result-badge {
            position: fixed;
            top: 15px;
            right: 140px;
            background: var(--dark-purple);
            border: 2px solid var(--primary-cyan);
            color: var(--primary-cyan);
            padding: 6px 18px;
            border-radius: 20px;
            font-family: 'Cinzel', serif;
            font-size: 1.1rem;
            font-weight: bold;
            box-shadow: 0 0 15px rgba(135, 232, 203, 0.4);
            z-index: 20;
            pointer-events: none;
            transition: all 0.3s ease;
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
            .roll-result-badge { right: 20px; top: 70px; }
        }
    </style>
</head>
<body>

    <canvas id="webgl-bg"></canvas>

    <div class="roll-result-badge" id="roll-badge">D20: Ready</div>
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
                <div class="skill-tag">🏰 สร้างแมพมายคราฟ (Minecraft World Building)</div>
                <div class="skill-tag">🦊 การทำ Figura มายคราฟ (Minecraft Figura Modding)</div>
                <div class="skill-tag">🕹️ 3D & Game Art</div>
            </div>

        </div>
    </div>

    <!-- Three.jsปัญหานี้มักเกิดจาก **โครงสร้างการวางโค้ด** หรือ **การสะกดชื่อตัวแปร/ฟังก์ชัน** ใน JavaScript ไม่ตรงกันครับ (เช่น ปุ่มกดได้เพราะรับ Event ได้ แต่ฟังก์ชันสุ่มไม่ถูกเรียก หรือไม่ได้สั่งให้เอาค่าไปแสดงผลใน HTML)

นี่คือจุดที่ต้องเช็ค 3 จุดหลัก พร้อมแนวทางแก้ไขครับ:

---

### 1. เช็คว่าได้ใส่ `.innerText` หรือ `.innerHTML` เพื่อแสดงผลหรือยัง?
บางครั้งระบบสุ่มเลขเรียบร้อยแล้ว แต่ลืมสั่งให้มันนำค่าที่สุ่มได้ไปเขียนลงในหน้าเว็บ

```html
<!-- HTML -->
<h1 id="result">0</h1>
<button id="myBtn">สุ่มเลข</button>
