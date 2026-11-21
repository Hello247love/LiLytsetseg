<!DOCTYPE html>
<html lang="mn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Neon Binary Lily - Masterpiece</title>
    <style>
        * {
            box-sizing: border-box;
        }
        body {
            margin: 0;
            padding: 0;
            background-color: #030108;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow: hidden;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        canvas {
            width: 100vw;
            height: 100vh;
            display: block;
            cursor: crosshair;
        }
        .controls {
            position: fixed;
            bottom: 24px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 10px;
            padding: 10px 18px;
            background: rgba(15, 10, 25, 0.65);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.15);
            border-radius: 50px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.6);
            z-index: 10;
        }
        .theme-btn {
            border: none;
            padding: 8px 16px;
            border-radius: 25px;
            color: #fff;
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            gap: 6px;
            background: rgba(255, 255, 255, 0.08);
        }
        .theme-btn:hover {
            transform: translateY(-2px);
            background: rgba(255, 255, 255, 0.18);
        }
        .theme-btn.active {
            box-shadow: 0 0 15px currentColor;
            background: rgba(255, 255, 255, 0.22);
        }
        .dot {
            width: 10px;
            height: 10px;
            border-radius: 50%;
            display: inline-block;
        }
        #musicToggle {
            position: fixed;
            bottom: 24px;
            right: 24px;
            padding: 10px 16px;
            border-radius: 50px;
            background: rgba(15, 10, 25, 0.65);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.15);
            color: #fff;
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            z-index: 10;
            transition: 0.3s;
        }
        #musicToggle:hover {
            background: rgba(255, 255, 255, 0.2);
        }
        #yt-player-container {
            position: fixed;
            width: 1px;
            height: 1px;
            opacity: 0;
            pointer-events: none;
            top: -100px;
            left: -100px;
        }
    </style>
</head>
<body>

<div id="yt-player-container">
    <div id="ytPlayer"></div>
</div>

<canvas id="lilyCanvas"></canvas>

<div class="controls">
    <button class="theme-btn active" onclick="setTheme('crimson', this)" style="color: #ff66a3;">
        <span class="dot" style="background: #ff2a85;"></span> Рояал Ягаан
    </button>
    <button class="theme-btn" onclick="setTheme('cyber', this)" style="color: #00f2fe;">
        <span class="dot" style="background: #00f2fe;"></span> Кибер Цэнхэр
    </button>
    <button class="theme-btn" onclick="setTheme('gold', this)" style="color: #ffd700;">
        <span class="dot" style="background: #ffaa00;"></span> Алтан Гэрэл
    </button>
    <button class="theme-btn" onclick="setTheme('aurora', this)" style="color: #b565ff;">
        <span class="dot" style="background: #9d4edd;"></span> Аврора Нил Ягаан
    </button>
</div>

<button id="musicToggle" onclick="toggleMusic()">🔇 Дуу асаах</button>

<script>
    // ================= 1. YOUTUBE BACKGROUND MUSIC =================
    let player;
    let isPlaying = false;
    const musicBtn = document.getElementById('musicToggle');

    const tag = document.createElement('script');
    tag.src = "https://www.youtube.com/iframe_api";
    const firstScriptTag = document.getElementsByTagName('script')[0];
    firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);

    function onYouTubeIframeAPIReady() {
        player = new YT.Player('ytPlayer', {
            height: '1',
            width: '1',
            videoId: 'ox4tmEV6-QU',
            playerVars: {
                'autoplay': 1,
                'loop': 1,
                'playlist': 'ox4tmEV6-QU',
                'controls': 0,
                'showinfo': 0,
                'rel': 0,
                'start': 0
            },
            events: {
                'onReady': onPlayerReady,
                'onStateChange': onPlayerStateChange
            }
        });
    }

    function onPlayerReady(event) {
        event.target.setVolume(70);
        event.target.seekTo(0);
        event.target.playVideo();

        const unlockAudio = () => {
            if (player && typeof player.playVideo === 'function') {
                player.unMute();
                player.seekTo(0);
                player.playVideo();
                isPlaying = true;
                musicBtn.textContent = "🔊 Дуу: Асаалттай";
            }
            ["click", "touchstart", "keydown", "pointerdown"].forEach(evt =>
                window.removeEventListener(evt, unlockAudio)
            );
        };

        ["click", "touchstart", "keydown", "pointerdown"].forEach(evt =>
            window.addEventListener(evt, unlockAudio, { passive: true })
        );
    }

    function onPlayerStateChange(event) {
        if (event.data === YT.PlayerState.PLAYING) {
            isPlaying = true;
            musicBtn.textContent = "🔊 Дуу: Асаалттай";
        } else if (event.data === YT.PlayerState.PAUSED) {
            isPlaying = false;
            musicBtn.textContent = "🔇 Дуу: Хаалттай";
        }
    }

    function toggleMusic() {
        if (!player || typeof player.playVideo !== 'function') return;
        if (isPlaying) {
            player.pauseVideo();
        } else {
            player.unMute();
            player.playVideo();
        }
    }

    // ================= 2. ЦЭЦЭГ БА BACKGROUND АНИМАЦ =================
    const canvas = document.getElementById('lilyCanvas');
    const ctx = canvas.getContext('2d');

    let W = canvas.width = window.innerWidth;
    let H = canvas.height = window.innerHeight;

    window.addEventListener('resize', () => {
        W = canvas.width = window.innerWidth;
        H = canvas.height = window.innerHeight;
    });

    const THEMES = {
        crimson: {
            bgTop: '#06010d',
            bgBottom: '#160314',
            nebula1: 'rgba(217, 38, 114, 0.22)',
            nebula2: 'rgba(72, 12, 168, 0.18)',
            petalInner: '#3d0219',
            petalMid: '#e02475',
            petalOuter: '#ffffff',
            petalEdge: '#ffffff',
            petalVein: '#ff9ec6',
            leafInner: '#041404',
            leafMid: '#1c6313',
            leafOuter: '#84e83c',
            leafEdge: '#b8ff66',
            stamenTip: '#ffaa00',
            dustColor: '#ff7eb3',
            rainColor: 'rgba(255, 105, 180, 0.14)'
        },
        cyber: {
            bgTop: '#010712',
            bgBottom: '#031926',
            nebula1: 'rgba(0, 242, 254, 0.22)',
            nebula2: 'rgba(79, 172, 254, 0.18)',
            petalInner: '#031d44',
            petalMid: '#0088ff',
            petalOuter: '#e0fbfc',
            petalEdge: '#ffffff',
            petalVein: '#72efdd',
            leafInner: '#021c1e',
            leafMid: '#007f5f',
            leafOuter: '#55a630',
            leafEdge: '#80ed99',
            stamenTip: '#ff007f',
            dustColor: '#00f2fe',
            rainColor: 'rgba(0, 242, 254, 0.14)'
        },
        gold: {
            bgTop: '#0a0601',
            bgBottom: '#1f1203',
            nebula1: 'rgba(255, 170, 0, 0.22)',
            nebula2: 'rgba(180, 60, 10, 0.18)',
            petalInner: '#3b1502',
            petalMid: '#e87a00',
            petalOuter: '#fff8e7',
            petalEdge: '#ffffff',
            petalVein: '#ffd166',
            leafInner: '#081c15',
            leafMid: '#2d6a4f',
            leafOuter: '#74c69d',
            leafEdge: '#b7e4c7',
            stamenTip: '#ff2a55',
            dustColor: '#ffd166',
            rainColor: 'rgba(255, 200, 80, 0.14)'
        },
        aurora: {
            bgTop: '#05010f',
            bgBottom: '#130529',
            nebula1: 'rgba(157, 78, 221, 0.25)',
            nebula2: 'rgba(0, 245, 212, 0.16)',
            petalInner: '#240046',
            petalMid: '#9d4edd',
            petalOuter: '#f3d5ff',
            petalEdge: '#ffffff',
            petalVein: '#e0aaff',
            leafInner: '#031926',
            leafMid: '#0077b6',
            leafOuter: '#00f5d4',
            leafEdge: '#90e0ef',
            stamenTip: '#ffbe0b',
            dustColor: '#c77dff',
            rainColor: 'rgba(180, 100, 255, 0.15)'
        }
    };

    let currentTheme = THEMES.crimson;
    let mouse = { x: -1000, y: -1000 };
    let growthProgress = 0; // Доороос дээш ургах явцыг хянах хувьсагч

    canvas.addEventListener('mousemove', (e) => {
        mouse.x = e.clientX;
        mouse.y = e.clientY;
    });
    canvas.addEventListener('mouseleave', () => {
        mouse.x = -1000;
        mouse.y = -1000;
    });

    function lerpColor(c1, c2, t) {
        t = Math.max(0, Math.min(1, t));
        const r1 = parseInt(c1.slice(1, 3), 16), g1 = parseInt(c1.slice(3, 5), 16), b1 = parseInt(c1.slice(5, 7), 16);
        const r2 = parseInt(c2.slice(1, 3), 16), g2 = parseInt(c2.slice(3, 5), 16), b2 = parseInt(c2.slice(5, 7), 16);
        return `rgb(${Math.round(r1 + (r2 - r1) * t)},${Math.round(g1 + (g2 - g1) * t)},${Math.round(b1 + (b2 - b1) * t)})`;
    }

    function getBezierPoint(t, p0, p1, p2, p3) {
        const u = 1 - t;
        const tt = t * t, uu = u * u;
        const uuu = uu * u, ttt = tt * t;
        return {
            x: uuu * p0.x + 3 * uu * t * p1.x + 3 * u * tt * p2.x + ttt * p3.x,
            y: uuu * p0.y + 3 * uu * t * p1.y + 3 * u * tt * p2.y + ttt * p3.y
        };
    }

    const rainDrops = Array.from({ length: 65 }, () => ({
        x: Math.random() * 2000,
        y: Math.random() * 1200,
        speed: 0.6 + Math.random() * 1.4,
        size: 10 + Math.random() * 6,
        char: Math.random() > 0.5 ? '1' : '0',
        opacity: 0.2 + Math.random() * 0.6
    }));

    const floatingDust = Array.from({ length: 85 }, () => ({
        x: Math.random() * 2000,
        y: Math.random() * 1200,
        vx: (Math.random() - 0.5) * 0.5,
        vy: -0.3 - Math.random() * 0.8,
        size: 1.5 + Math.random() * 3,
        phase: Math.random() * Math.PI * 2,
        isDigit: Math.random() > 0.65
    }));

    const stars = Array.from({ length: 120 }, () => ({
        x: Math.random(),
        y: Math.random(),
        size: Math.random() * 1.8,
        phase: Math.random() * Math.PI * 2
    }));

    let lilyParticles = [];

    function buildLily() {
        lilyParticles = [];
        const th = currentTheme;

        function addShape(leftCurve, rightCurve, config) {
            const {
                rows = 60, innerColor, midColor, outerColor, edgeColor,
                veinColor, hasVein = false, fontSizeBase = 10, density = 0.88, layer = 1
            } = config;

            for (let i = 0; i <= rows; i++) {
                const t = i / rows;
                const pLeft = getBezierPoint(t, ...leftCurve);
                const pRight = getBezierPoint(t, ...rightCurve);
                const dist = Math.hypot(pRight.x - pLeft.x, pRight.y - pLeft.y);
                const currentCols = Math.max(2, Math.floor(dist / 7.5));

                for (let j = 0; j <= currentCols; j++) {
                    const s = j / currentCols;
                    const isEdge = (j === 0 || j === currentCols || i === rows);
                    const isCenterVein = hasVein && Math.abs(s - 0.5) < 0.08 && t > 0.15 && t < 0.92;

                    if (!isEdge && !isCenterVein && Math.random() > density) continue;

                    const x = pLeft.x + (pRight.x - pLeft.x) * s;
                    const y = pLeft.y + (pRight.y - pLeft.y) * s;

                    let color;
                    if (isEdge) {
                        color = t < 0.2 ? lerpColor(midColor, edgeColor, t * 5) : edgeColor;
                    } else if (isCenterVein) {
                        color = lerpColor(midColor, veinColor, t);
                    } else {
                        color = t < 0.42
                            ? lerpColor(innerColor, midColor, t / 0.42)
                            : lerpColor(midColor, outerColor, (t - 0.42) / 0.58);
                        const edgeFactor = Math.abs(s - 0.5) * 2;
                        if (edgeFactor > 0.55) {
                            color = lerpColor(color, edgeColor, (edgeFactor - 0.55) * 2.1);
                        }
                    }

                    const alpha = isEdge ? 1 : (0.6 + Math.random() * 0.4);
                    const size = isEdge
                        ? fontSizeBase + 2.5
                        : (isCenterVein ? fontSizeBase + 1 : fontSizeBase * (0.68 + Math.random() * 0.5));

                    lilyParticles.push({
                        baseX: x - 480,
                        baseY: y - 480,
                        color,
                        alpha,
                        size: Math.round(size),
                        phase: Math.random() * Math.PI * 2,
                        isGlow: isEdge || isCenterVein,
                        layer,
                        partT: t // Дэлбээ болон навчны ургалтын дараалал
                    });
                }
            }
        }

        function addStamen(curve) {
            const steps = 45;
            for (let i = 0; i <= steps; i++) {
                const t = i / steps;
                const p = getBezierPoint(t, ...curve);
                const color = t < 0.25 ? th.leafMid : (t < 0.78 ? th.leafOuter : th.stamenTip);
                lilyParticles.push({
                    baseX: p.x - 480,
                    baseY: p.y - 480,
                    color,
                    alpha: 0.95,
                    size: t > 0.85 ? 12 : 9,
                    phase: t * 5,
                    isGlow: t > 0.8,
                    layer: 3,
                    partT: t
                });
            }
            const tip = curve[3];
            for (let dx = -7; dx <= 7; dx += 3.5) {
                for (let dy = -14; dy <= 14; dy += 4.5) {
                    if (dx * dx / 49 + dy * dy / 196 <= 1) {
                        lilyParticles.push({
                            baseX: tip.x + dx - 480,
                            baseY: tip.y + dy - 480,
                            color: lerpColor(th.stamenTip, '#ffffff', Math.random() * 0.5),
                            alpha: 1,
                            size: 11,
                            phase: Math.random() * 6,
                            isGlow: true,
                            layer: 3,
                            partT: 1
                        });
                    }
                }
            }
        }

        const leafStyle = {
            innerColor: th.leafInner, midColor: th.leafMid, outerColor: th.leafOuter,
            edgeColor: th.leafEdge, veinColor: th.leafEdge, hasVein: true,
            fontSizeBase: 9.5, density: 0.82, layer: 1
        };

        // Гол ишийг доороос дээш ургахаар чиглэлийг нь (y: 920 -> 660) болгов
        addShape([{x: 460, y: 920}, {x: 456, y: 830}, {x: 452, y: 750}, {x: 445, y: 660}],
                 [{x: 482, y: 920}, {x: 484, y: 830}, {x: 482, y: 750}, {x: 478, y: 660}], { ...leafStyle, rows: 45, density: 0.92 });
        addShape([{x: 265, y: 400}, {x: 195, y: 280}, {x: 115, y: 245}, {x: 25, y: 325}],
                 [{x: 285, y: 380}, {x: 215, y: 245}, {x: 120, y: 205}, {x: 25, y: 325}], { ...leafStyle, rows: 55 });
        addShape([{x: 448, y: 730}, {x: 365, y: 550}, {x: 215, y: 530}, {x: 135, y: 725}],
                 [{x: 456, y: 700}, {x: 375, y: 510}, {x: 215, y: 490}, {x: 135, y: 725}], { ...leafStyle, rows: 60 });
        addShape([{x: 480, y: 770}, {x: 545, y: 635}, {x: 675, y: 635}, {x: 755, y: 845}],
                 [{x: 485, y: 810}, {x: 555, y: 685}, {x: 680, y: 685}, {x: 755, y: 845}], { ...leafStyle, rows: 55 });

        addShape([{x: 260, y: 245}, {x: 155, y: 215}, {x: 115, y: 120}, {x: 118, y: 20}],
                 [{x: 270, y: 235}, {x: 250, y: 130}, {x: 195, y: 50}, {x: 118, y: 20}],
                 { innerColor: th.leafMid, midColor: th.petalMid, outerColor: th.petalOuter, edgeColor: th.leafEdge, hasVein: true, veinColor: th.petalVein, rows: 48, density: 0.92, layer: 1 });

        const backPetal = {
            innerColor: th.petalInner, midColor: th.petalMid, outerColor: th.petalOuter,
            edgeColor: th.petalEdge, veinColor: th.petalVein, hasVein: true,
            fontSizeBase: 10, density: 0.88, layer: 2
        };

        addShape([{x: 475, y: 440}, {x: 445, y: 270}, {x: 480, y: 135}, {x: 560, y: 90}],
                 [{x: 530, y: 450}, {x: 630, y: 315}, {x: 675, y: 180}, {x: 560, y: 90}], { ...backPetal, rows: 65 });
        addShape([{x: 460, y: 465}, {x: 310, y: 400}, {x: 225, y: 295}, {x: 230, y: 190}],
                 [{x: 488, y: 435}, {x: 430, y: 245}, {x: 335, y: 140}, {x: 230, y: 190}], { ...backPetal, rows: 65 });
        addShape([{x: 525, y: 465}, {x: 600, y: 315}, {x: 705, y: 230}, {x: 815, y: 295}],
                 [{x: 545, y: 500}, {x: 695, y: 430}, {x: 800, y: 380}, {x: 815, y: 295}], { ...backPetal, rows: 65 });

        addStamen([{x: 508, y: 475}, {x: 525, y: 375}, {x: 540, y: 300}, {x: 545, y: 255}]);
        addStamen([{x: 518, y: 480}, {x: 555, y: 385}, {x: 582, y: 318}, {x: 595, y: 275}]);
        addStamen([{x: 530, y: 492}, {x: 595, y: 418}, {x: 645, y: 365}, {x: 678, y: 335}]);
        addStamen([{x: 495, y: 485}, {x: 485, y: 395}, {x: 480, y: 330}, {x: 490, y: 295}]);

        const frontPetal = {
            innerColor: th.petalInner, midColor: th.petalMid, outerColor: th.petalOuter,
            edgeColor: th.petalEdge, veinColor: th.petalVein, hasVein: true,
            fontSizeBase: 10.5, density: 0.94, layer: 4
        };

        addShape([{x: 475, y: 495}, {x: 335, y: 605}, {x: 195, y: 575}, {x: 168, y: 480}],
                 [{x: 470, y: 465}, {x: 305, y: 390}, {x: 145, y: 390}, {x: 168, y: 480}], { ...frontPetal, rows: 65 });
        addShape([{x: 540, y: 495}, {x: 685, y: 410}, {x: 850, y: 440}, {x: 855, y: 575}],
                 [{x: 530, y: 532}, {x: 650, y: 635}, {x: 785, y: 645}, {x: 855, y: 575}], { ...frontPetal, rows: 65 });
        addShape([{x: 482, y: 510}, {x: 395, y: 605}, {x: 430, y: 710}, {x: 510, y: 765}],
                 [{x: 530, y: 510}, {x: 610, y: 605}, {x: 588, y: 710}, {x: 510, y: 765}], { ...frontPetal, rows: 60 });

        // Бүх цэгүүдэд доороос дээш ургах дарааллын утга (revealOrder: 0.0 -> 1.0) оноох
        for (let p of lilyParticles) {
            const heightNorm = Math.max(0, Math.min(1, (440 - p.baseY) / 900));
            const layerDelay = (p.layer - 1) * 0.07;
            p.revealOrder = Math.min(1, heightNorm * 0.72 + layerDelay + p.partT * 0.12);
        }

        lilyParticles.sort((a, b) => a.layer - b.layer);
    }

    function setTheme(themeKey, btn) {
        currentTheme = THEMES[themeKey];
        document.querySelectorAll('.theme-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        buildLily();
    }

    buildLily();

    let time = 0;

    function render() {
        time += 0.025;
        const th = currentTheme;

        // Доороос дээш ургах явцыг зөөлөн нэмэгдүүлэх
        if (growthProgress < 1.2) {
            growthProgress += 0.0055;
        }

        const bgGrad = ctx.createLinearGradient(0, 0, 0, H);
        bgGrad.addColorStop(0, th.bgTop);
        bgGrad.addColorStop(1, th.bgBottom);
        ctx.fillStyle = bgGrad;
        ctx.fillRect(0, 0, W, H);

        const cx = W / 2;
        const cy = H / 2 - 15;
        const scale = Math.min(W, H) / 960;

        const neb1 = ctx.createRadialGradient(cx, cy, 30, cx, cy, 480 * scale);
        neb1.addColorStop(0, th.nebula1);
        neb1.addColorStop(0.55, th.nebula2);
        neb1.addColorStop(1, 'rgba(0,0,0,0)');
        ctx.fillStyle = neb1;
        ctx.fillRect(0, 0, W, H);

        ctx.save();
        ctx.translate(cx, cy + 20 * scale);
        ctx.rotate(time * 0.15);
        ctx.strokeStyle = th.rainColor;
        ctx.lineWidth = 1.2;
        ctx.setLineDash([6, 14]);
        ctx.beginPath();
        ctx.arc(0, 0, 340 * scale, 0, Math.PI * 2);
        ctx.stroke();

        ctx.rotate(-time * 0.3);
        ctx.setLineDash([2, 10]);
        ctx.beginPath();
        ctx.arc(0, 0, 385 * scale, 0, Math.PI * 2);
        ctx.stroke();
        ctx.restore();

        for (let s of stars) {
            const alpha = 0.25 + 0.55 * Math.sin(time * 1.5 + s.phase);
            ctx.fillStyle = `rgba(255, 255, 255, ${alpha})`;
            ctx.beginPath();
            ctx.arc(s.x * W, s.y * H, s.size, 0, Math.PI * 2);
            ctx.fill();
        }

        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';
        for (let r of rainDrops) {
            r.y += r.speed;
            if (r.y > H + 20) {
                r.y = -20;
                r.x = Math.random() * W;
            }
            ctx.font = `${Math.round(r.size)}px "Courier New", monospace`;
            ctx.fillStyle = th.rainColor;
            ctx.fillText(r.char, r.x, r.y);
        }

        // 6. САРААНА ЦЭЦЭГ ДООРООС ДЭЭШ УРГАЖ ГАРЧ ИРЭХ
        for (let i = 0; i < lilyParticles.length; i++) {
            const p = lilyParticles[i];

            // Ургах ээлж нь хараахан болоогүй цэгийг алгасах
            if (growthProgress < p.revealOrder) continue;

            // Тухайн цэгийн ургаж тодрох хувь (0 -> 1)
            const localGrowth = Math.min(1, (growthProgress - p.revealOrder) / 0.16);
            const ease = 1 - Math.pow(1 - localGrowth, 3); // Зөөлөн удааширч тогтох (ease-out)

            const swayX = Math.sin(time + p.baseY * 0.008) * (p.layer * 0.9);
            const swayY = Math.cos(time + p.baseX * 0.008) * (p.layer * 0.6);

            // Доороос дээш өргөгдөж, цэцгийн голоос гадагш дэлгэрч буй мэт шилжилт
            const riseOffset = (1 - ease) * 35;
            const bloomFactor = 0.78 + 0.22 * ease;

            let x = cx + (p.baseX * bloomFactor + swayX) * scale;
            let y = cy + (p.baseY * bloomFactor + riseOffset + swayY) * scale;

            const dx = x - mouse.x;
            const dy = y - mouse.y;
            const dist = Math.hypot(dx, dy);
            let extraScale = 0;

            if (dist < 120) {
                const force = (120 - dist) / 120;
                x += (dx / dist) * force * 16;
                y += (dy / dist) * force * 16;
                extraScale = force * 5.5;
            }

            const twinkle = 0.78 + 0.22 * Math.sin(time * 2.2 + p.phase);
            ctx.globalAlpha = p.alpha * twinkle * ease;

            const fontSize = Math.max(4, Math.round((p.size * (0.4 + 0.6 * ease) + extraScale) * scale));
            ctx.font = `bold ${fontSize}px "Courier New", monospace`;
            ctx.fillStyle = extraScale > 2.2 ? '#ffffff' : p.color;

            if (p.isGlow || extraScale > 1.5 || localGrowth < 0.85) {
                ctx.shadowColor = p.color;
                ctx.shadowBlur = extraScale > 1.5 ? 14 : (localGrowth < 0.85 ? 10 : 6);
            } else {
                ctx.shadowBlur = 0;
            }

            ctx.fillText('1', x, y);
        }

        ctx.shadowBlur = 0;

        for (let d of floatingDust) {
            d.x += d.vx + Math.sin(time + d.phase) * 0.3;
            d.y += d.vy;
            if (d.y < -20) {
                d.y = H + 20;
                d.x = Math.random() * W;
            }

            const pulse = 0.3 + 0.7 * Math.abs(Math.sin(time * 1.5 + d.phase));
            ctx.globalAlpha = pulse;
            ctx.fillStyle = th.dustColor;
            ctx.shadowColor = th.dustColor;
            ctx.shadowBlur = 8;

            if (d.isDigit) {
                ctx.font = 'bold 10px "Courier New", monospace';
                ctx.fillText('1', d.x, d.y);
            } else {
                ctx.beginPath();
                ctx.arc(d.x, d.y, d.size, 0, Math.PI * 2);
                ctx.fill();
            }
        }

        ctx.globalAlpha = 1;
        ctx.shadowBlur = 0;

        requestAnimationFrame(render);
    }

    render();
</script>

</body>
</html>
