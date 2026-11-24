[code_artifact.html](https://github.com/user-attachments/files/35329399/code_artifact.html)
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>탑 쌓기 미니 게임</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Jua & Pretendard -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Jua&family=Noto+Sans+KR:wght@400;500;700;900&display=swap" rel="stylesheet">
    <!-- Tone.js for sound effects -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.8.49/Tone.js"></script>
    <style>
        :root {
            --bg-gradient: linear-gradient(135deg, #FFF9F0 0%, #FFE8CC 50%, #FFD8A8 100%);
            --panel-bg: rgba(255, 255, 255, 0.90);
            --accent-color: #FF8C42;
            --accent-hover: #F76707;
            --text-main: #5C3A21;
            --border-color: #FFC078;
        }

        body {
            font-family: 'Noto Sans KR', sans-serif;
            background: var(--bg-gradient);
            color: var(--text-main);
            transition: background 0.6s ease, color 0.4s ease;
            touch-action: manipulation;
            user-select: none;
            -webkit-user-select: none;
            overflow: hidden;
        }

        .font-game {
            font-family: 'Jua', sans-serif;
        }

        .theme-panel {
            background: var(--panel-bg);
            backdrop-filter: blur(14px);
            -webkit-backdrop-filter: blur(14px);
            border: 2px solid var(--border-color);
            box-shadow: 0 8px 20px -4px color-mix(in srgb, var(--accent-color) 16%, transparent);
            transition: background 0.4s ease, border-color 0.4s ease, box-shadow 0.4s ease;
        }

        .theme-btn {
            background-color: var(--accent-color);
            color: #ffffff;
            box-shadow: 0 4px 0 var(--accent-hover), 0 8px 15px -3px color-mix(in srgb, var(--accent-color) 40%, transparent);
            transition: transform 0.12s ease, background-color 0.2s ease, box-shadow 0.12s ease;
        }

        .theme-btn:hover {
            background-color: var(--accent-hover);
        }

        .theme-btn:active {
            transform: translateY(3px) scale(0.97);
            box-shadow: 0 1px 0 var(--accent-hover);
        }

        .theme-text-accent {
            color: var(--accent-color);
            transition: color 0.4s ease;
        }

        .theme-border-active {
            border-color: var(--accent-color) !important;
            background-color: #ffffff !important;
            transform: translateY(-2px);
            box-shadow: 0 4px 10px color-mix(in srgb, var(--accent-color) 25%, transparent);
        }

        .bg-particle {
            position: fixed;
            pointer-events: none;
            z-index: 0;
            opacity: 0.72;
            filter: drop-shadow(0 3px 6px rgba(0, 0, 0, 0.12));
            animation: floatUp linear infinite;
        }

        .bg-dot-pattern {
            background-image: radial-gradient(color-mix(in srgb, var(--accent-color) 28%, transparent) 3px, transparent 3px);
            background-size: 26px 26px;
        }

        .bg-cloud {
            position: fixed;
            background: #ffffff;
            border: 2px solid color-mix(in srgb, var(--accent-color) 35%, #ffffff);
            box-shadow: 0 6px 14px color-mix(in srgb, var(--accent-color) 18%, transparent);
            border-radius: 9999px;
            pointer-events: none;
            z-index: 0;
            animation: driftCloud linear infinite;
        }

        .bg-cloud::before,
        .bg-cloud::after {
            content: '';
            position: absolute;
            background: #ffffff;
            border-radius: 50%;
        }

        .bg-cloud::before {
            width: 55%;
            height: 140%;
            top: -55%;
            left: 16%;
        }

        .bg-cloud::after {
            width: 42%;
            height: 115%;
            top: -38%;
            right: 18%;
        }

        @keyframes driftCloud {
            0% { transform: translateX(-25vw); }
            100% { transform: translateX(125vw); }
        }

        @keyframes floatUp {
            0% { transform: translateY(110vh) rotate(-12deg) scale(0.95); }
            50% { transform: translateY(45vh) rotate(12deg) scale(1.05); }
            100% { transform: translateY(-15vh) rotate(-12deg) scale(0.95); }
        }

        @keyframes pulseGlow {
            0%, 100% { transform: scale(1) translateY(0); opacity: 0.98; }
            50% { transform: scale(1.03) translateY(-3px); opacity: 1; }
        }
        .animate-pulse-glow {
            animation: pulseGlow 2.2s infinite ease-in-out;
        }

        canvas {
            display: block;
            width: 100%;
            height: 100%;
        }
    </style>
</head>
<body class="h-screen w-screen flex flex-col justify-between relative bg-dot-pattern">

    <!-- Cute Clouds & Background Particles -->
    <div id="bg-clouds-container" class="fixed inset-0 overflow-hidden pointer-events-none z-0">
        <div class="bg-cloud" style="width: 110px; height: 36px; top: 14%; animation-duration: 34s; animation-delay: -5s;"></div>
        <div class="bg-cloud" style="width: 145px; height: 46px; top: 32%; animation-duration: 42s; animation-delay: -18s; opacity: 0.55;"></div>
        <div class="bg-cloud" style="width: 95px; height: 32px; top: 64%; animation-duration: 30s; animation-delay: -12s; opacity: 0.6;"></div>
        <div class="bg-cloud" style="width: 130px; height: 42px; top: 82%; animation-duration: 38s; animation-delay: -24s; opacity: 0.5;"></div>
    </div>
    <div id="bg-particles-container" class="fixed inset-0 overflow-hidden pointer-events-none z-0"></div>

    <!-- Top HUD / Status Bar -->
    <header class="relative z-20 w-full max-w-2xl mx-auto px-3 pt-3 sm:px-4 sm:pt-4">
        <div class="theme-panel rounded-2xl shadow-lg px-3 py-2.5 sm:px-5 sm:py-3">
            <div class="flex items-center justify-between gap-2 mb-2 pb-2 border-b border-black/10">
                <div class="flex items-center gap-2">
                    <span id="hud-theme-icon" class="text-2xl">🍕</span>
                    <h1 class="font-game text-lg sm:text-xl tracking-wide">탑 쌓기 미니 게임</h1>
                </div>
                <div class="flex items-center gap-2">
                    <button id="btn-sound" type="button" title="효과음 켜기/끄기"
                        class="p-1.5 rounded-xl bg-black/5 hover:bg-black/10 transition text-sm font-bold flex items-center justify-center w-8 h-8 cursor-pointer">
                        🔊
                    </button>
                    <span id="status-badge"
                        class="px-3 py-1 rounded-full text-xs sm:text-sm font-bold bg-amber-500/20 text-amber-800 border border-amber-500/30 transition-colors">
                        대기 중
                    </span>
                </div>
            </div>

            <div class="grid grid-cols-3 gap-2 text-center">
                <div class="bg-black/5 rounded-xl py-1.5 px-2">
                    <div class="text-[11px] sm:text-xs opacity-70 font-medium">현재 층수</div>
                    <div class="font-game text-xl sm:text-2xl theme-text-accent leading-tight">
                        <span id="hud-score">0</span><span class="text-xs sm:text-sm ml-0.5 font-sans font-bold">층</span>
                    </div>
                </div>
                <div class="bg-black/5 rounded-xl py-1.5 px-2">
                    <div class="text-[11px] sm:text-xs opacity-70 font-medium">플레이 시간</div>
                    <div id="hud-time" class="font-game text-xl sm:text-2xl leading-tight">00:00</div>
                </div>
                <div class="bg-black/5 rounded-xl py-1.5 px-2">
                    <div class="text-[11px] sm:text-xs opacity-70 font-medium">최고 기록</div>
                    <div class="font-game text-xl sm:text-2xl text-amber-600 leading-tight">
                        <span id="hud-best">0</span><span class="text-xs sm:text-sm ml-0.5 font-sans font-bold">층</span>
                    </div>
                </div>
            </div>
        </div>
    </header>

    <!-- Main Game Area -->
    <main class="relative z-10 flex-1 w-full max-w-2xl mx-auto px-3 py-2 sm:px-4 flex flex-col items-center justify-center overflow-hidden">
        <div id="canvas-wrapper" class="relative w-full h-full rounded-3xl overflow-hidden theme-panel shadow-xl border-2">
            <canvas id="gameCanvas"></canvas>

            <!-- Start Overlay -->
            <div id="overlay-start" class="absolute inset-0 flex flex-col items-center justify-center bg-black/20 backdrop-blur-[2px] p-4 text-center transition-opacity duration-300">
                <div class="theme-panel rounded-3xl p-6 sm:p-8 max-w-xs w-full shadow-2xl animate-pulse-glow">
                    <div id="start-preview-icon" class="text-5xl mb-3">🍕</div>
                    <h2 class="font-game text-2xl sm:text-3xl mb-2">탑을 높이 쌓아보세요!</h2>
                    <p class="text-xs sm:text-sm opacity-80 mb-5 leading-relaxed">
                        화면을 <strong>터치</strong>, <strong>클릭</strong>하거나<br>
                        <strong>스페이스바</strong>를 누르면 블록이 떨어집니다.<br>
                        정확히 맞추면 <span class="font-bold theme-text-accent">완벽!</span> 판정 발동!
                    </p>
                    <button id="btn-start" type="button"
                        class="theme-btn w-full py-3.5 px-6 rounded-2xl font-game text-xl shadow-lg cursor-pointer">
                        게임 시작하기
                    </button>
                </div>
            </div>

            <!-- Game Over Overlay -->
            <div id="overlay-gameover" class="hidden absolute inset-0 flex flex-col items-center justify-center bg-black/40 backdrop-blur-sm p-4 text-center">
                <div class="theme-panel rounded-3xl p-6 sm:p-7 max-w-xs w-full shadow-2xl transform transition-all">
                    <div id="new-record-badge" class="hidden inline-block px-3 py-1 rounded-full bg-yellow-400 text-yellow-950 font-black text-xs mb-2 shadow">
                        🎉 신기록 달성! 🎉
                    </div>
                    <h2 class="font-game text-3xl sm:text-4xl mb-1 text-rose-600">게임 오버</h2>
                    <p class="text-xs sm:text-sm opacity-75 mb-4">블록이 탑에서 벗어났습니다!</p>

                    <div class="grid grid-cols-2 gap-2.5 bg-black/5 rounded-2xl p-3 mb-5">
                        <div>
                            <div class="text-xs opacity-70">최종 기록</div>
                            <div class="font-game text-2xl theme-text-accent"><span id="final-score">0</span>층</div>
                        </div>
                        <div>
                            <div class="text-xs opacity-70">최고 기록</div>
                            <div class="font-game text-2xl text-amber-600"><span id="final-best">0</span>층</div>
                        </div>
                        <div>
                            <div class="text-xs opacity-70">플레이 시간</div>
                            <div id="final-time" class="font-bold text-sm mt-0.5">00:00</div>
                        </div>
                        <div>
                            <div class="text-xs opacity-70">최대 연속 완벽</div>
                            <div class="font-bold text-sm mt-0.5"><span id="final-combo">0</span>연속</div>
                        </div>
                    </div>

                    <button id="btn-restart" type="button"
                        class="theme-btn w-full py-3.5 px-6 rounded-2xl font-game text-xl shadow-lg cursor-pointer">
                        다시 도전하기
                    </button>
                </div>
            </div>

            <!-- Tap Hint -->
            <div id="tap-hint" class="hidden pointer-events-none absolute bottom-4 inset-x-0 text-center">
                <span class="px-3.5 py-1.5 rounded-full bg-black/40 text-white text-xs font-medium backdrop-blur-sm">
                    화면을 터치하거나 클릭하여 블록 떨어뜨리기
                </span>
            </div>
        </div>
    </main>

    <!-- Bottom Theme Selection & Customization Bar -->
    <footer class="relative z-20 w-full max-w-2xl mx-auto px-3 pb-3 sm:px-4 sm:pb-4">
        <div class="theme-panel rounded-2xl shadow-lg p-2.5 sm:p-3">
            <div class="flex items-center justify-between mb-2 px-1">
                <span class="text-xs sm:text-sm font-bold flex items-center gap-1.5">
                    <span>🎨 블록 디자인 & 테마 선택</span>
                    <span id="current-theme-label" class="text-xs font-normal opacity-75">(피자 테마)</span>
                </span>
                <span id="custom-img-status" class="text-[11px] opacity-80 hidden sm:inline-flex items-center gap-1.5 font-medium">
                    <span>기본/아이콘 모드</span>
                </span>
            </div>

            <div class="flex items-center gap-1.5 sm:gap-2 overflow-x-auto pb-0.5">
                <button type="button" data-theme="pizza"
                    class="theme-choice-btn flex-1 min-w-[50px] py-2 px-1 rounded-xl bg-white/80 border-2 border-transparent hover:bg-white transition flex flex-col items-center justify-center gap-0.5 cursor-pointer theme-border-active">
                    <span class="text-xl sm:text-2xl leading-none">🍕</span>
                    <span class="text-[10px] font-bold">피자</span>
                </button>
                <button type="button" data-theme="fish"
                    class="theme-choice-btn flex-1 min-w-[50px] py-2 px-1 rounded-xl bg-white/80 border-2 border-transparent hover:bg-white transition flex flex-col items-center justify-center gap-0.5 cursor-pointer">
                    <span class="text-xl sm:text-2xl leading-none">🐟</span>
                    <span class="text-[10px] font-bold">물고기</span>
                </button>
                <button type="button" data-theme="brick"
                    class="theme-choice-btn flex-1 min-w-[50px] py-2 px-1 rounded-xl bg-white/80 border-2 border-transparent hover:bg-white transition flex flex-col items-center justify-center gap-0.5 cursor-pointer">
                    <span class="text-xl sm:text-2xl leading-none">🧸</span>
                    <span class="text-[10px] font-bold">곰돌이</span>
                </button>
                <button type="button" data-theme="star"
                    class="theme-choice-btn flex-1 min-w-[50px] py-2 px-1 rounded-xl bg-white/80 border-2 border-transparent hover:bg-white transition flex flex-col items-center justify-center gap-0.5 cursor-pointer">
                    <span class="text-xl sm:text-2xl leading-none">⭐</span>
                    <span class="text-[10px] font-bold">별</span>
                </button>
                <button type="button" data-theme="strawberry"
                    class="theme-choice-btn flex-1 min-w-[50px] py-2 px-1 rounded-xl bg-white/80 border-2 border-transparent hover:bg-white transition flex flex-col items-center justify-center gap-0.5 cursor-pointer">
                    <span class="text-xl sm:text-2xl leading-none">🍓</span>
                    <span class="text-[10px] font-bold">딸기</span>
                </button>

                <!-- Custom Image Upload -->
                <label for="custom-image-input" id="btn-custom-upload"
                    class="flex-1 min-w-[62px] py-2 px-1 rounded-xl bg-white/80 border-2 border-dashed border-gray-400 hover:bg-white transition flex flex-col items-center justify-center gap-0.5 cursor-pointer relative overflow-hidden">
                    <img id="custom-thumb" src="" alt="미리보기" class="hidden w-5 h-5 rounded object-cover" />
                    <span id="custom-upload-icon" class="text-xl sm:text-2xl leading-none">🖼️</span>
                    <span class="text-[10px] font-bold whitespace-nowrap">내 사진</span>
                </label>
                <input type="file" id="custom-image-input" accept="image/*" class="hidden" />
            </div>
        </div>
    </footer>

    <!-- Toast Message Box -->
    <div id="toast-box" class="fixed top-24 left-1/2 -translate-x-1/2 z-50 pointer-events-none opacity-0 transition-opacity duration-300">
        <div id="toast-message" class="px-4 py-2 rounded-full bg-gray-900/90 text-white text-xs sm:text-sm font-medium shadow-lg"></div>
    </div>

    <script>
        /**
         * ============================================================================
         * 1. 테마 및 색상 정의
         * ============================================================================
         */
        const THEMES = {
            pizza: {
                id: 'pizza',
                name: '치즈 피자 테마',
                icon: '🍕',
                decorIcons: ['🍕', '🧀', '🍅', '🍄', '💛'],
                bgGradient: 'linear-gradient(135deg, #FFF9F0 0%, #FFE8CC 50%, #FFD8A8 100%)',
                panelBg: 'rgba(255, 255, 255, 0.90)',
                accent: '#FF8C42',
                accentHover: '#F76707',
                textMain: '#5C3A21',
                borderColor: '#FFC078',
                blockColors: ['#FFD166', '#FFB703', '#FF9F1C', '#FFE066'],
                blockBorder: '#D97706',
                detailColor: '#F59E0B',
                accentColor2: '#FFF3BF'
            },
            fish: {
                id: 'fish',
                name: '몽글 아쿠아 테마',
                icon: '🐳',
                decorIcons: ['🐳', '🐟', '🫧', '🐚', '🩵'],
                bgGradient: 'linear-gradient(135deg, #F0F9FF 0%, #D0EBFF 50%, #A5D8FF 100%)',
                panelBg: 'rgba(255, 255, 255, 0.90)',
                accent: '#38BDF8',
                accentHover: '#0284C7',
                textMain: '#1E4E68',
                borderColor: '#74C0FC',
                blockColors: ['#74C0FC', '#4DABF7', '#66D9E8', '#99E9F2'],
                blockBorder: '#1971C2',
                detailColor: '#228BE6',
                accentColor2: '#E7F5FF'
            },
            brick: {
                id: 'brick',
                name: '귀여운 곰돌이 테마',
                icon: '🧸',
                decorIcons: ['🧸', '🍯', '🎈', '🍪', '🤎'],
                bgGradient: 'linear-gradient(135deg, #FFF8F0 0%, #FDEBD0 50%, #F5CBA7 100%)',
                panelBg: 'rgba(255, 255, 255, 0.92)',
                accent: '#C07A48',
                accentHover: '#A05E30',
                textMain: '#4E2E18',
                borderColor: '#E5B893',
                blockColors: ['#D99B66', '#C8844E', '#E3AC7B', '#B8733E'],
                blockBorder: '#7E4A25',
                detailColor: '#FFF8F0',
                accentColor2: '#FAE5D3'
            },
            star: {
                id: 'star',
                name: '솜사탕 별님 테마',
                icon: '🦄',
                decorIcons: ['🦄', '⭐', '🌙', '✨', '💜'],
                bgGradient: 'linear-gradient(135deg, #F8F0FC 0%, #F3D9FA 50%, #E599F7 100%)',
                panelBg: 'rgba(255, 255, 255, 0.90)',
                accent: '#CC5DE8',
                accentHover: '#AE3EC9',
                textMain: '#4A1D5E',
                borderColor: '#E599F7',
                blockColors: ['#DA77F2', '#B197FC', '#F783AC', '#E599F7'],
                blockBorder: '#862E9C',
                detailColor: '#9C36B5',
                accentColor2: '#F8F0FC'
            },
            strawberry: {
                id: 'strawberry',
                name: '딸기 생크림 테마',
                icon: '🍓',
                decorIcons: ['🍓', '🍰', '🌸', '🎀', '🩷'],
                bgGradient: 'linear-gradient(135deg, #FFF0F6 0%, #FFDEEB 50%, #FCC2D7 100%)',
                panelBg: 'rgba(255, 255, 255, 0.90)',
                accent: '#F06595',
                accentHover: '#D6336C',
                textMain: '#591C33',
                borderColor: '#FAA2C1',
                blockColors: ['#FF85A1', '#F783AC', '#FFA8C5', '#FFB3C6'],
                blockBorder: '#C2255C',
                detailColor: '#E64980',
                accentColor2: '#FFF0F6'
            }
        };

        /**
         * ============================================================================
         * 2. 게임 상태 관리
         * ============================================================================
         */
        const STORAGE_KEY = 'tower_stack_best_score_v2';

        const state = {
            status: 'IDLE', // 'IDLE' | 'PLAYING' | 'GAMEOVER'
            score: 0,
            bestScore: 0,
            combo: 0,
            maxCombo: 0,
            startTime: 0,
            elapsedSeconds: 0,
            timerInterval: null,

            currentThemeId: 'pizza',
            customImage: null,
            customTheme: null,
            useCustomImage: false,
            soundEnabled: true,

            width: 400,
            height: 600,
            blockHeight: 34,
            initialBlockWidth: 180,

            blocks: [],
            activeBlock: null,
            fallingPieces: [],
            effects: [],
            cameraY: 0,
            targetCameraY: 0,
            screenFlash: 0
        };

        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const canvasWrapper = document.getElementById('canvas-wrapper');

        const hudScore = document.getElementById('hud-score');
        const hudTime = document.getElementById('hud-time');
        const hudBest = document.getElementById('hud-best');
        const statusBadge = document.getElementById('status-badge');
        const hudThemeIcon = document.getElementById('hud-theme-icon');
        const startPreviewIcon = document.getElementById('start-preview-icon');
        const currentThemeLabel = document.getElementById('current-theme-label');

        const overlayStart = document.getElementById('overlay-start');
        const overlayGameOver = document.getElementById('overlay-gameover');
        const tapHint = document.getElementById('tap-hint');

        const finalScoreEl = document.getElementById('final-score');
        const finalBestEl = document.getElementById('final-best');
        const finalTimeEl = document.getElementById('final-time');
        const finalComboEl = document.getElementById('final-combo');
        const newRecordBadge = document.getElementById('new-record-badge');

        /**
         * ============================================================================
         * 5. 테마 적용 및 커스텀 이미지 색상 추출
         * ============================================================================
         */
        function rgbToHsl(r, g, b) {
            r /= 255; g /= 255; b /= 255;
            const max = Math.max(r, g, b), min = Math.min(r, g, b);
            let h = 0, s = 0, l = (max + min) / 2;
            if (max !== min) {
                const d = max - min;
                s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
                switch (max) {
                    case r: h = (g - b) / d + (g < b ? 6 : 0); break;
                    case g: h = (b - r) / d + 2; break;
                    case b: h = (r - g) / d + 4; break;
                }
                h /= 6;
            }
            return { h: Math.round(h * 360), s: Math.round(s * 100), l: Math.round(l * 100) };
        }

        function extractThemeFromImage(img) {
            const sampleCanvas = document.createElement('canvas');
            const size = 50;
            sampleCanvas.width = size;
            sampleCanvas.height = size;
            const sCtx = sampleCanvas.getContext('2d');
            sCtx.drawImage(img, 0, 0, size, size);

            const data = sCtx.getImageData(0, 0, size, size).data;
            let rSum = 0, gSum = 0, bSum = 0, weightSum = 0;
            let allR = 0, allG = 0, allB = 0, totalCount = 0;

            for (let i = 0; i < data.length; i += 4) {
                if (data[i + 3] < 128) continue;
                const r = data[i], g = data[i + 1], b = data[i + 2];
                allR += r; allG += g; allB += b; totalCount++;

                const max = Math.max(r, g, b);
                const min = Math.min(r, g, b);
                const sat = max === 0 ? 0 : (max - min) / max;
                const lum = (0.299 * r + 0.587 * g + 0.114 * b) / 255;

                // 순백색 및 순흑색 픽셀 제외하고 채도가 있는 대표 색상 가중치 부여
                if (lum > 0.12 && lum < 0.90) {
                    const weight = 0.25 + sat * 2.5;
                    rSum += r * weight;
                    gSum += g * weight;
                    bSum += b * weight;
                    weightSum += weight;
                }
            }

            const domR = weightSum > 0 ? Math.round(rSum / weightSum) : Math.round(allR / Math.max(1, totalCount));
            const domG = weightSum > 0 ? Math.round(gSum / weightSum) : Math.round(allG / Math.max(1, totalCount));
            const domB = weightSum > 0 ? Math.round(bSum / weightSum) : Math.round(allB / Math.max(1, totalCount));

            const { h, s } = rgbToHsl(domR, domG, domB);
            const isMonochrome = s < 12;
            const safeSat = isMonochrome ? 10 : Math.min(82, Math.max(42, s));

            const primaryHex = `hsl(${h}, ${safeSat}%, 44%)`;
            const hoverHex = `hsl(${h}, ${safeSat}%, 36%)`;
            const borderHex = `hsl(${h}, ${Math.min(85, safeSat + 5)}%, 72%)`;
            const textMain = `hsl(${h}, ${Math.min(60, safeSat)}%, 16%)`;
            const bgGradient = `linear-gradient(135deg, hsl(${h}, ${Math.min(75, safeSat)}%, 96%) 0%, hsl(${h}, ${Math.min(75, safeSat)}%, 91%) 50%, hsl(${(h + 15) % 360}, ${Math.min(75, safeSat)}%, 84%) 100%)`;

            return {
                id: 'custom',
                name: '내 사진 맞춤 테마',
                icon: '🖼️',
                decorIcons: ['🖼️', '✨', '🎨', '💫', '🌟'],
                bgGradient: bgGradient,
                panelBg: 'rgba(255, 255, 255, 0.88)',
                accent: primaryHex,
                accentHover: hoverHex,
                textMain: textMain,
                borderColor: borderHex,
                swatchColor: `rgb(${domR}, ${domG}, ${domB})`,
                blockBorder: `hsl(${h}, ${safeSat}%, 28%)`
            };
        }

        function applyTheme(themeId) {
            const presetTheme = THEMES[themeId] || THEMES.pizza;
            state.currentThemeId = presetTheme.id;

            const activeTheme = (state.useCustomImage && state.customTheme) ? state.customTheme : presetTheme;

            const root = document.documentElement;
            root.style.setProperty('--bg-gradient', activeTheme.bgGradient);
            root.style.setProperty('--panel-bg', activeTheme.panelBg);
            root.style.setProperty('--accent-color', activeTheme.accent);
            root.style.setProperty('--accent-hover', activeTheme.accentHover);
            root.style.setProperty('--text-main', activeTheme.textMain);
            root.style.setProperty('--border-color', activeTheme.borderColor);

            hudThemeIcon.textContent = state.useCustomImage ? '🖼️' : presetTheme.icon;
            startPreviewIcon.textContent = state.useCustomImage ? '🖼️' : presetTheme.icon;
            currentThemeLabel.textContent = state.useCustomImage
                ? `(사진 테마 자동 적용됨 🎨)`
                : `(${presetTheme.name})`;

            const statusEl = document.getElementById('custom-img-status');
            if (state.useCustomImage && state.customTheme) {
                statusEl.innerHTML = `<span class="inline-block w-3 h-3 rounded-full border border-black/20 shadow-sm" style="background:${state.customTheme.swatchColor}"></span><span>사진 테마 자동 적용됨 🎨</span>`;
            } else {
                statusEl.textContent = '아이콘 테마 적용 중';
            }

            document.querySelectorAll('.theme-choice-btn').forEach(btn => {
                if (!state.useCustomImage && btn.dataset.theme === themeId) {
                    btn.classList.add('theme-border-active');
                } else {
                    btn.classList.remove('theme-border-active');
                }
            });

            renderBackgroundParticles(activeTheme.decorIcons);
        }

        /**
         * ============================================================================
         * 3. 효과음 (Tone.js)
         * ============================================================================
         */
        let synth = null;

        function initAudio() {
            if (!synth && window.Tone) {
                synth = new Tone.PolySynth(Tone.Synth, {
                    oscillator: { type: 'triangle' },
                    envelope: { attack: 0.01, decay: 0.1, sustain: 0.1, release: 0.2 }
                }).toDestination();
                synth.volume.value = -8;
            }
        }

        function playSound(type, combo = 0) {
            if (!state.soundEnabled || !window.Tone) return;
            try {
                if (Tone.context.state !== 'running') {
                    Tone.start();
                }
                initAudio();
                if (!synth) return;

                const now = Tone.now();
                if (type === 'drop') {
                    synth.triggerAttackRelease('C4', '16n', now);
                } else if (type === 'perfect') {
                    const notes = ['C5', 'D5', 'E5', 'G5', 'A5', 'C6', 'D6', 'E6', 'G6'];
                    const note = notes[Math.min(combo - 1, notes.length - 1)];
                    synth.triggerAttackRelease(note, '16n', now);
                    synth.triggerAttackRelease('G5', '32n', now + 0.05);
                } else if (type === 'gameover') {
                    synth.triggerAttackRelease('E4', '8n', now);
                    synth.triggerAttackRelease('C4', '4n', now + 0.15);
                }
            } catch (e) {
                // Ignore audio context errors
            }
        }

        /**
         * ============================================================================
         * 4. 최고 점수 로컬 스토리지 관리
         * ============================================================================
         */
        function loadBestScore() {
            try {
                const saved = localStorage.getItem(STORAGE_KEY);
                state.bestScore = saved ? parseInt(saved, 10) || 0 : 0;
            } catch (e) {
                state.bestScore = 0;
            }
            hudBest.textContent = state.bestScore;
        }

        function saveBestScore(newScore) {
            if (newScore > state.bestScore) {
                state.bestScore = newScore;
                hudBest.textContent = state.bestScore;
                try {
                    localStorage.setItem(STORAGE_KEY, String(state.bestScore));
                } catch (e) {}
                return true;
            }
            return false;
        }

        /**
         * ============================================================================
         * 5-2. 배경 파티클 및 알림 메시지
         * ============================================================================
         */
        function renderBackgroundParticles(icons) {
            const container = document.getElementById('bg-particles-container');
            container.innerHTML = '';
            const count = 20;
            for (let i = 0; i < count; i++) {
                const el = document.createElement('div');
                el.className = 'bg-particle';
                el.textContent = icons[i % icons.length];
                el.style.left = `${(i * 5.3) % 95}%`;
                el.style.fontSize = `${22 + (i % 4) * 9}px`;
                el.style.animationDuration = `${11 + (i % 6) * 2.5}s`;
                el.style.animationDelay = `${-(i * 1.4)}s`;
                container.appendChild(el);
            }
        }

        function showToast(msg) {
            const box = document.getElementById('toast-box');
            const text = document.getElementById('toast-message');
            text.textContent = msg;
            box.classList.remove('opacity-0');
            clearTimeout(box._timeout);
            box._timeout = setTimeout(() => {
                box.classList.add('opacity-0');
            }, 2200);
        }

        document.querySelectorAll('.theme-choice-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                e.stopPropagation();
                const themeId = btn.dataset.theme;
                state.useCustomImage = false;
                document.getElementById('btn-custom-upload').classList.remove('theme-border-active');
                applyTheme(themeId);
                showToast(`${THEMES[themeId].icon} ${THEMES[themeId].name}로 변경되었습니다!`);
            });
        });

        const customImageInput = document.getElementById('custom-image-input');
        customImageInput.addEventListener('change', (e) => {
            const file = e.target.files && e.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = (event) => {
                const img = new Image();
                img.onload = () => {
                    state.customImage = img;
                    state.customTheme = extractThemeFromImage(img);
                    state.useCustomImage = true;

                    const thumb = document.getElementById('custom-thumb');
                    const uploadIcon = document.getElementById('custom-upload-icon');
                    thumb.src = event.target.result;
                    thumb.classList.remove('hidden');
                    uploadIcon.classList.add('hidden');

                    document.getElementById('btn-custom-upload').classList.add('theme-border-active');
                    applyTheme(state.currentThemeId);
                    showToast('🎨 사진 색상을 분석해 맞춤 테마가 자동 적용되었습니다!');
                };
                img.src = event.target.result;
            };
            reader.readAsDataURL(file);
        });

        document.getElementById('btn-sound').addEventListener('click', (e) => {
            e.stopPropagation();
            state.soundEnabled = !state.soundEnabled;
            e.currentTarget.textContent = state.soundEnabled ? '🔊' : '🔇';
            showToast(state.soundEnabled ? '효과음이 켜졌습니다.' : '효과음이 꺼졌습니다.');
        });

        /**
         * ============================================================================
         * 6. 캔버스 리사이즈 및 렌더링 초기화
         * ============================================================================
         */
        function resizeCanvas() {
            const rect = canvasWrapper.getBoundingClientRect();
            const dpr = Math.min(window.devicePixelRatio || 1, 2);
            state.width = rect.width;
            state.height = rect.height;

            canvas.width = Math.floor(rect.width * dpr);
            canvas.height = Math.floor(rect.height * dpr);
            ctx.setTransform(dpr, 0, 0, dpr, 0, 0);

            state.initialBlockWidth = Math.min(200, Math.max(130, state.width * 0.46));
            state.blockHeight = Math.max(28, Math.min(36, Math.floor(state.height * 0.065)));

            if (state.status === 'IDLE') {
                setupInitialTower();
            }
        }

        function formatTime(sec) {
            const m = Math.floor(sec / 60);
            const s = sec % 60;
            return `${String(m).padStart(2, '0')}:${String(s).padStart(2, '0')}`;
        }

        function updateStatusUI() {
            hudScore.textContent = state.score;
            hudTime.textContent = formatTime(state.elapsedSeconds);

            if (state.status === 'IDLE') {
                statusBadge.textContent = '대기 중';
                statusBadge.className = 'px-3 py-1 rounded-full text-xs sm:text-sm font-bold bg-amber-500/20 text-amber-800 border border-amber-500/30';
            } else if (state.status === 'PLAYING') {
                statusBadge.textContent = '플레이 중';
                statusBadge.className = 'px-3 py-1 rounded-full text-xs sm:text-sm font-bold bg-emerald-500/20 text-emerald-800 border border-emerald-500/40';
            } else {
                statusBadge.textContent = '게임 오버';
                statusBadge.className = 'px-3 py-1 rounded-full text-xs sm:text-sm font-bold bg-rose-500/20 text-rose-800 border border-rose-500/40';
            }
        }

        /**
         * ============================================================================
         * 7. 게임 플레이 로직 & 판정 시스템
         * ============================================================================
         */
        function setupInitialTower() {
            const baseWidth = state.initialBlockWidth;
            const baseX = (state.width - baseWidth) / 2;
            const baseY = state.height - state.blockHeight * 2.2;

            state.blocks = [{
                x: baseX,
                y: baseY,
                width: baseWidth,
                height: state.blockHeight,
                colorIdx: 0,
                isPerfect: false
            }];
            state.activeBlock = null;
            state.fallingPieces = [];
            state.effects = [];
            state.cameraY = 0;
            state.targetCameraY = 0;
        }

        function startGame() {
            initAudio();
            state.status = 'PLAYING';
            state.score = 0;
            state.combo = 0;
            state.maxCombo = 0;
            state.elapsedSeconds = 0;
            state.startTime = Date.now();

            overlayStart.classList.add('hidden');
            overlayGameOver.classList.add('hidden');
            tapHint.classList.remove('hidden');

            setupInitialTower();
            spawnNextBlock();
            updateStatusUI();

            clearInterval(state.timerInterval);
            state.timerInterval = setInterval(() => {
                if (state.status === 'PLAYING') {
                    state.elapsedSeconds = Math.floor((Date.now() - state.startTime) / 1000);
                    hudTime.textContent = formatTime(state.elapsedSeconds);
                }
            }, 250);
        }

        function spawnNextBlock() {
            const topBlock = state.blocks[state.blocks.length - 1];
            const level = state.blocks.length;

            // Progressive speed increase based on stack height
            const baseSpeed = state.width * 0.44;
            const speedMultiplier = 1 + Math.min(level * 0.06, 2.5);
            const speed = baseSpeed * speedMultiplier;

            const fromLeft = level % 2 === 1;
            const startX = fromLeft ? -topBlock.width * 0.5 : state.width - topBlock.width * 0.5;
            const dir = fromLeft ? 1 : -1;

            state.activeBlock = {
                x: startX,
                y: topBlock.y - state.blockHeight,
                width: topBlock.width,
                height: state.blockHeight,
                dir: dir,
                speed: speed,
                colorIdx: level
            };

            const desiredScreenY = state.height * 0.52;
            if (state.activeBlock.y + state.targetCameraY < desiredScreenY) {
                state.targetCameraY = desiredScreenY - state.activeBlock.y;
            }
        }

        function dropBlock() {
            if (state.status !== 'PLAYING' || !state.activeBlock) return;

            const active = state.activeBlock;
            const top = state.blocks[state.blocks.length - 1];

            const deltaX = active.x - top.x;
            const absDelta = Math.abs(deltaX);
            const perfectThreshold = Math.max(7, top.width * 0.065);

            // Complete miss
            if (absDelta >= top.width) {
                state.fallingPieces.push({
                    x: active.x,
                    y: active.y,
                    width: active.width,
                    height: active.height,
                    vx: deltaX > 0 ? 90 : -90,
                    vy: 50,
                    rot: 0,
                    vrot: deltaX > 0 ? 2.5 : -2.5,
                    colorIdx: active.colorIdx
                });
                state.activeBlock = null;
                triggerGameOver();
                return;
            }

            // '완벽' (Perfect) judgment
            if (absDelta <= perfectThreshold) {
                state.combo += 1;
                state.maxCombo = Math.max(state.maxCombo, state.combo);

                let newWidth = top.width;
                let newX = top.x;
                if (state.combo >= 3 && newWidth < state.initialBlockWidth) {
                    const bonus = Math.min(12, state.initialBlockWidth - newWidth);
                    newWidth += bonus;
                    newX = Math.max(0, Math.min(state.width - newWidth, newX - bonus / 2));
                }

                state.blocks.push({
                    x: newX,
                    y: active.y,
                    width: newWidth,
                    height: active.height,
                    colorIdx: active.colorIdx,
                    isPerfect: true
                });

                state.score += 1;
                state.screenFlash = 0.35;
                playSound('perfect', state.combo);

                const comboText = state.combo > 1 ? `완벽! ✨ ${state.combo}연속` : '완벽! ✨';
                state.effects.push({
                    type: 'text',
                    x: newX + newWidth / 2,
                    y: active.y - 10,
                    text: comboText,
                    alpha: 1,
                    vy: -55
                });
                state.effects.push({
                    type: 'ring',
                    x: newX,
                    y: active.y,
                    width: newWidth,
                    height: active.height,
                    alpha: 0.9,
                    expand: 0
                });
            } else {
                // Imperfect placement: slice half of the overhang (50% penalty)
                state.combo = 0;
                const trimWidth = absDelta * 0.5;
                const overlapWidth = top.width - trimWidth;

                if (overlapWidth < 6) {
                    state.fallingPieces.push({
                        x: active.x,
                        y: active.y,
                        width: active.width,
                        height: active.height,
                        vx: deltaX > 0 ? 90 : -90,
                        vy: 50,
                        rot: 0,
                        vrot: deltaX > 0 ? 2.5 : -2.5,
                        colorIdx: active.colorIdx
                    });
                    state.activeBlock = null;
                    triggerGameOver();
                    return;
                }

                // Align cleanly with the supporting block's edge while keeping half of the overhang
                const placedX = deltaX > 0 ? (top.x + trimWidth) : top.x;

                state.blocks.push({
                    x: placedX,
                    y: active.y,
                    width: overlapWidth,
                    height: active.height,
                    colorIdx: active.colorIdx,
                    isPerfect: false
                });

                const trimX = deltaX > 0 ? (placedX + overlapWidth) : (top.x - trimWidth);
                state.fallingPieces.push({
                    x: trimX,
                    y: active.y,
                    width: trimWidth,
                    height: active.height,
                    vx: deltaX > 0 ? 75 : -75,
                    vy: 20,
                    rot: 0,
                    vrot: deltaX > 0 ? 2.2 : -2.2,
                    colorIdx: active.colorIdx
                });

                state.score += 1;
                playSound('drop');
            }

            updateStatusUI();
            if (state.score === 1) {
                tapHint.classList.add('hidden');
            }
            spawnNextBlock();
        }

        function triggerGameOver() {
            state.status = 'GAMEOVER';
            clearInterval(state.timerInterval);
            tapHint.classList.add('hidden');
            playSound('gameover');

            const isNewRecord = saveBestScore(state.score);
            updateStatusUI();

            finalScoreEl.textContent = state.score;
            finalBestEl.textContent = state.bestScore;
            finalTimeEl.textContent = formatTime(state.elapsedSeconds);
            finalComboEl.textContent = state.maxCombo;

            if (isNewRecord && state.score > 0) {
                newRecordBadge.classList.remove('hidden');
            } else {
                newRecordBadge.classList.add('hidden');
            }

            setTimeout(() => {
                overlayGameOver.classList.remove('hidden');
            }, 380);
        }

        /**
         * ============================================================================
         * 8. 블록 커스텀 드로잉 엔진
         * ============================================================================
         */
        function drawBlock(block, isFallingPiece = false) {
            const theme = THEMES[state.currentThemeId] || THEMES.pizza;
            const { x, y, width, height, colorIdx } = block;

            ctx.save();
            const radius = Math.min(10, height * 0.28, width * 0.25);
            ctx.beginPath();
            ctx.roundRect(x, y, width, height, radius);
            ctx.clip();

            if (state.useCustomImage && state.customImage) {
                const img = state.customImage;
                const imgRatio = img.width / img.height;
                const blockRatio = width / height;
                let sx = 0, sy = 0, sw = img.width, sh = img.height;

                if (imgRatio > blockRatio) {
                    sw = img.height * blockRatio;
                    sx = (img.width - sw) / 2;
                } else {
                    sh = img.width / blockRatio;
                    sy = (img.height - sh) / 2;
                }
                ctx.drawImage(img, sx, sy, sw, sh, x, y, width, height);

                ctx.fillStyle = 'rgba(255, 255, 255, 0.22)';
                ctx.fillRect(x, y, width, 4);
                ctx.fillStyle = 'rgba(0, 0, 0, 0.14)';
                ctx.fillRect(x, y + height - 4, width, 4);
            } else {
                const baseColor = theme.blockColors[colorIdx % theme.blockColors.length];
                ctx.fillStyle = baseColor;
                ctx.fillRect(x, y, width, height);

                ctx.fillStyle = 'rgba(255, 255, 255, 0.28)';
                ctx.fillRect(x, y, width, 4);

                ctx.fillStyle = 'rgba(0, 0, 0, 0.12)';
                ctx.fillRect(x, y + height - 4, width, 4);

                if (!isFallingPiece && width >= 40) {
                    ctx.font = `${Math.floor(height * 0.58)}px sans-serif`;
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    ctx.fillText(theme.icon, x + width / 2, y + height / 2 + 1);
                }
            }

            ctx.restore();

            ctx.save();
            ctx.beginPath();
            ctx.roundRect(x, y, width, height, radius);
            ctx.lineWidth = 2;
            ctx.strokeStyle = (state.useCustomImage && state.customTheme)
                ? state.customTheme.blockBorder
                : theme.blockBorder;
            ctx.stroke();
            ctx.restore();
        }

        /**
         * ============================================================================
         * 9. 메인 게임 루프 (60fps)
         * ============================================================================
         */
        let lastTimestamp = 0;

        function update(dt) {
            state.cameraY += (state.targetCameraY - state.cameraY) * Math.min(1, dt * 8);

            if (state.screenFlash > 0) {
                state.screenFlash = Math.max(0, state.screenFlash - dt * 1.5);
            }

            if (state.status === 'PLAYING' && state.activeBlock) {
                const b = state.activeBlock;
                b.x += b.dir * b.speed * dt;

                const minX = -b.width * 0.35;
                const maxX = state.width - b.width * 0.65;
                if (b.x <= minX) {
                    b.x = minX;
                    b.dir = 1;
                } else if (b.x >= maxX) {
                    b.x = maxX;
                    b.dir = -1;
                }
            }

            for (let i = state.fallingPieces.length - 1; i >= 0; i--) {
                const p = state.fallingPieces[i];
                p.x += p.vx * dt;
                p.y += p.vy * dt;
                p.vy += 980 * dt;
                p.rot += p.vrot * dt;

                if (p.y + state.cameraY > state.height + 150) {
                    state.fallingPieces.splice(i, 1);
                }
            }

            for (let i = state.effects.length - 1; i >= 0; i--) {
                const eff = state.effects[i];
                if (eff.type === 'text') {
                    eff.y += eff.vy * dt;
                    eff.alpha -= dt * 1.2;
                } else if (eff.type === 'ring') {
                    eff.expand += dt * 160;
                    eff.alpha -= dt * 1.8;
                }
                if (eff.alpha <= 0) {
                    state.effects.splice(i, 1);
                }
            }
        }

        function render() {
            ctx.clearRect(0, 0, state.width, state.height);

            // 캔버스 내부 귀여운 일러스트 배경 렌더링
            drawCuteCanvasBackground();

            ctx.save();
            ctx.translate(0, state.cameraY);

            for (let i = 0; i < state.blocks.length; i++) {
                drawBlock(state.blocks[i], false);
            }

            if (state.status === 'PLAYING' && state.activeBlock) {
                drawBlock(state.activeBlock, false);
            }

            for (let i = 0; i < state.fallingPieces.length; i++) {
                const p = state.fallingPieces[i];
                ctx.save();
                ctx.translate(p.x + p.width / 2, p.y + p.height / 2);
                ctx.rotate(p.rot);
                ctx.translate(-(p.x + p.width / 2), -(p.y + p.height / 2));
                drawBlock(p, true);
                ctx.restore();
            }

            for (let i = 0; i < state.effects.length; i++) {
                const eff = state.effects[i];
                if (eff.type === 'text') {
                    ctx.save();
                    ctx.font = 'bold 20px Jua, sans-serif';
                    ctx.fillStyle = `rgba(234, 88, 12, ${eff.alpha})`;
                    ctx.strokeStyle = `rgba(255, 255, 255, ${eff.alpha})`;
                    ctx.lineWidth = 3;
                    ctx.textAlign = 'center';
                    ctx.strokeText(eff.text, eff.x, eff.y);
                    ctx.fillText(eff.text, eff.x, eff.y);
                    ctx.restore();
                } else if (eff.type === 'ring') {
                    ctx.save();
                    ctx.beginPath();
                    ctx.roundRect(
                        eff.x - eff.expand / 2,
                        eff.y - eff.expand / 4,
                        eff.width + eff.expand,
                        eff.height + eff.expand / 2,
                        12
                    );
                    ctx.strokeStyle = `rgba(245, 158, 11, ${eff.alpha})`;
                    ctx.lineWidth = 3;
                    ctx.stroke();
                    ctx.restore();
                }
            }

            ctx.restore();

            if (state.screenFlash > 0) {
                ctx.save();
                ctx.fillStyle = `rgba(255, 220, 100, ${state.screenFlash * 0.35})`;
                ctx.fillRect(0, 0, state.width, state.height);
                ctx.restore();
            }
        }

        /**
         * 캔버스 내부에 선명하게 보이는 귀여운 동화풍 배경(무지개, 구름, 별, 언덕) 그리기
         */
        function drawCuteCanvasBackground() {
            const theme = THEMES[state.currentThemeId] || THEMES.pizza;
            const w = state.width;
            const h = state.height;
            const t = performance.now() * 0.001;

            ctx.save();

            // 1. 은은한 파스텔 무지개 아치
            const rainbowColors = [
                'rgba(255, 135, 135, 0.25)',
                'rgba(255, 192, 120, 0.25)',
                'rgba(255, 236, 153, 0.28)',
                'rgba(140, 233, 154, 0.25)',
                'rgba(116, 192, 252, 0.25)',
                'rgba(218, 119, 242, 0.22)'
            ];
            const arcCenterX = w * 0.5;
            const arcCenterY = h * 0.45;
            const baseRadius = Math.min(w, h) * 0.48;
            rainbowColors.forEach((col, idx) => {
                ctx.beginPath();
                ctx.arc(arcCenterX, arcCenterY, baseRadius - idx * 9, Math.PI, 0, false);
                ctx.strokeStyle = col;
                ctx.lineWidth = 8;
                ctx.stroke();
            });

            // 2. 반짝이는 귀여운 별 & 하트 장식
            const stars = [
                { x: w * 0.16, y: h * 0.16, size: 16, phase: 0 },
                { x: w * 0.84, y: h * 0.20, size: 18, phase: 1.5 },
                { x: w * 0.22, y: h * 0.42, size: 14, phase: 3.1 },
                { x: w * 0.78, y: h * 0.48, size: 15, phase: 4.2 }
            ];
            stars.forEach((s) => {
                const scale = 0.85 + Math.sin(t * 2.5 + s.phase) * 0.2;
                ctx.save();
                ctx.translate(s.x, s.y);
                ctx.scale(scale, scale);
                ctx.font = `${s.size}px sans-serif`;
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillText('✨', 0, 0);
                ctx.restore();
            });

            // 3. 선명한 테두리가 있는 몽글몽글 구름 2개
            const drawCuteCloud = (cx, cy, scale) => {
                ctx.save();
                ctx.translate(cx, cy);
                ctx.scale(scale, scale);
                ctx.fillStyle = 'rgba(255, 255, 255, 0.92)';
                ctx.strokeStyle = theme.borderColor;
                ctx.lineWidth = 2.5;

                ctx.beginPath();
                ctx.arc(-22, 4, 16, Math.PI * 0.5, Math.PI * 1.5);
                ctx.arc(-4, -8, 20, Math.PI, Math.PI * 2);
                ctx.arc(18, -2, 16, Math.PI * 1.3, Math.PI * 0.3);
                ctx.arc(24, 8, 13, Math.PI * 1.5, Math.PI * 0.5);
                ctx.closePath();
                ctx.fill();
                ctx.stroke();
                ctx.restore();
            };

            const cloud1X = ((w * 0.25 + t * 12) % (w + 120)) - 60;
            const cloud2X = ((w * 0.75 + t * 8) % (w + 120)) - 60;
            drawCuteCloud(cloud1X, h * 0.22, 1.05);
            drawCuteCloud(cloud2X, h * 0.36, 0.85);

            // 4. 하단 몽글몽글 파스텔 언덕 (카메라 이동에 따라 자연스럽게 내려감)
            const hillOffsetY = Math.min(180, state.cameraY * 0.6);
            ctx.save();
            ctx.translate(0, hillOffsetY);

            // 뒤쪽 언덕
            ctx.beginPath();
            ctx.moveTo(0, h);
            ctx.lineTo(0, h - 65);
            ctx.quadraticCurveTo(w * 0.28, h - 115, w * 0.6, h - 65);
            ctx.quadraticCurveTo(w * 0.85, h - 30, w, h - 75);
            ctx.lineTo(w, h);
            ctx.closePath();
            ctx.fillStyle = theme.accentColor2 || 'rgba(255, 255, 255, 0.65)';
            ctx.fill();

            // 앞쪽 언덕
            ctx.beginPath();
            ctx.moveTo(0, h);
            ctx.lineTo(0, h - 42);
            ctx.quadraticCurveTo(w * 0.45, h - 85, w, h - 38);
            ctx.lineTo(w, h);
            ctx.closePath();
            ctx.fillStyle = 'rgba(255, 255, 255, 0.85)';
            ctx.fill();
            ctx.strokeStyle = theme.borderColor;
            ctx.lineWidth = 2;
            ctx.stroke();

            // 언덕 위 귀여운 테마 아이콘 장식
            ctx.font = '20px sans-serif';
            ctx.textAlign = 'center';
            ctx.fillText(theme.decorIcons[1] || '🌸', w * 0.14, h - 22);
            ctx.fillText(theme.decorIcons[2] || '🌷', w * 0.86, h - 20);

            ctx.restore();
            ctx.restore();
        }

        function gameLoop(timestamp) {
            if (!lastTimestamp) lastTimestamp = timestamp;
            const dt = Math.min((timestamp - lastTimestamp) / 1000, 0.05);
            lastTimestamp = timestamp;

            update(dt);
            render();
            requestAnimationFrame(gameLoop);
        }

        /**
         * ============================================================================
         * 10. 입력 이벤트 및 초기화
         * ============================================================================
         */
        document.getElementById('btn-start').addEventListener('click', (e) => {
            e.stopPropagation();
            startGame();
        });

        document.getElementById('btn-restart').addEventListener('click', (e) => {
            e.stopPropagation();
            startGame();
        });

        function handlePlayAction(e) {
            if (state.status !== 'PLAYING') return;
            if (e.target.closest('button') || e.target.closest('label') || e.target.closest('input')) {
                return;
            }
            if (e.type === 'touchstart') {
                e.preventDefault();
            }
            dropBlock();
        }

        canvasWrapper.addEventListener('mousedown', handlePlayAction);
        canvasWrapper.addEventListener('touchstart', handlePlayAction, { passive: false });

        window.addEventListener('keydown', (e) => {
            if (e.code === 'Space' || e.code === 'ArrowDown') {
                e.preventDefault();
                if (state.status === 'PLAYING') {
                    dropBlock();
                } else if (state.status === 'IDLE') {
                    startGame();
                } else if (state.status === 'GAMEOVER') {
                    startGame();
                }
            }
        });

        window.addEventListener('resize', () => {
            resizeCanvas();
        });

        window.addEventListener('load', () => {
            loadBestScore();
            applyTheme('pizza');
            resizeCanvas();
            updateStatusUI();
            requestAnimationFrame(gameLoop);
        });
    </script>
</body>
</html>
