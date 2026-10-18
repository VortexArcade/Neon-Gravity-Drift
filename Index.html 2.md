<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <title>Neon Gravity Drift</title>  
    <script src="https://cdn.tailwindcss.com"></script>  
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&display=swap" rel="stylesheet">  
    <style>  
        body {  
            font-family: 'Orbitron', sans-serif;  
            background-color: #030308;  
            color: #ffffff;  
            overflow: hidden;  
            user-select: none;  
            touch-action: none;  
        }  
        canvas {  
            display: block;  
            width: 100vw;  
            height: 100vh;  
            background: linear-gradient(135deg, #0a061a 0%, #030308 100%);  
        }  
        .glow-text {  
            text-shadow: 0 0 10px rgba(168, 85, 247, 0.7), 0 0 20px rgba(236, 72, 153, 0.5);  
        }  
        .glow-button {  
            box-shadow: 0 0 20px rgba(168, 85, 247, 0.5);  
            transition: all 0.2s ease;  
        }  
        .glow-button:hover {  
            box-shadow: 0 0 30px rgba(236, 72, 153, 0.9);  
            transform: scale(1.05);  
        }  
    </style>  
</head>  
<body class="relative w-screen h-screen">  
  
    <!-- HUD Overlay -->  
    <div id="hud" class="absolute top-0 left-0 w-full p-6 flex justify-between items-center pointer-events-none z-10 text-purple-400 font-bold tracking-widest text-lg md:text-2xl">  
        <div class="flex flex-col">  
            <span class="text-xs text-purple-500 uppercase">Distance / Level</span>  
            <div class="flex items-baseline gap-2">  
                <span id="scoreVal" class="glow-text">0m</span>  
                <span id="levelIndicator" class="text-xs text-cyan-400 bg-cyan-950/80 px-2 py-0.5 rounded-md border border-cyan-500/30">LVL 1</span>  
            </div>  
        </div>  
        <div class="flex flex-col items-center">  
            <span class="text-xs text-pink-500 uppercase">Crystals</span>  
            <span id="crystalVal" class="text-pink-400 text-xl">0</span>  
        </div>  
        <div class="flex flex-col items-end">  
            <span class="text-xs text-purple-500 uppercase">Speed</span>  
            <span id="speedVal" class="text-cyan-400 text-xl">1.0x</span>  
        </div>  
    </div>  
  
    <!-- Level Milestone Notification Banner -->  
    <div id="milestoneBanner" class="absolute top-24 left-1/2 -translate-x-1/2 bg-gradient-to-r from-purple-900/90 to-pink-900/90 border border-pink-500/60 px-6 py-2 rounded-xl text-center glow-text z-20 pointer-events-none opacity-0 transition-opacity duration-300">  
        <span id="milestoneText" class="text-sm md:text-lg font-black text-cyan-300 uppercase tracking-widest">LEVEL UP: HYPER DRIFT!</span>  
    </div>  
  
    <!-- Start Screen Overlay -->  
    <div id="startScreen" class="absolute inset-0 flex flex-col items-center justify-center bg-black/85 backdrop-blur-md z-30 px-4">  
        <h1 class="text-4xl md:text-7xl font-black text-transparent bg-clip-text bg-gradient-to-r from-purple-400 via-pink-500 to-cyan-400 glow-text mb-2 text-center">NEON GRAVITY DRIFT</h1>  
        <p class="text-cyan-400 text-xs md:text-sm font-semibold tracking-wider mb-4 uppercase">Infinite Escalation & Reward Tiers</p>  
        <p class="text-gray-400 text-sm md:text-base mb-6 text-center max-w-md">Start slow, survive milestones every 500m to unlock faster velocities and high-tier crystal rewards!</p>  
          
        <!-- Ship Customization / Rewards Preview -->  
        <div class="mb-6 bg-slate-900/80 border border-purple-500/30 p-4 rounded-xl flex items-center gap-4 max-w-sm w-full justify-between">  
            <div>  
                <span class="text-xs text-gray-400 uppercase tracking-wider block">Active Rig</span>  
                <span id="equippedSkin" class="text-sm font-bold text-pink-400">Cyan Pulse Pod</span>  
            </div>  
            <div id="skinBadge" class="w-8 h-8 rounded-full border-2 border-cyan-400 bg-cyan-950 flex items-center justify-center shadow-[0_0_10px_#06b6d4]"></div>  
        </div>  
  
        <button id="startBtn" class="glow-button px-10 py-5 bg-gradient-to-r from-purple-600 to-pink-600 text-white font-black text-xl rounded-2xl uppercase tracking-wider cursor-pointer">  
            Initiate Drift  
        </button>  
        <div class="mt-6 text-xs text-gray-500 text-center">  
            Controls: Press <span class="text-purple-400 font-bold">SPACEBAR</span> or <span class="text-purple-400 font-bold">TAP SCREEN</span> to Flip Gravity  
        </div>  
    </div>  
  
    <!-- Game Over Overlay -->  
    <div id="gameOverScreen" class="absolute inset-0 flex flex-col items-center justify-center bg-black/90 backdrop-blur-md z-30 hidden px-4">  
        <h2 class="text-4xl md:text-6xl font-black text-pink-500 mb-2 text-center" style="text-shadow: 0 0 20px rgba(236,72,153,0.8);">CRASH DETECTED</h2>  
        <div class="bg-slate-900/90 border border-purple-500/40 p-6 rounded-2xl max-w-md w-full mb-6 flex flex-col gap-3">  
            <div class="flex justify-between items-center border-b border-purple-500/20 pb-2">  
                <span class="text-gray-400 text-sm">Distance Reached:</span>  
                <span id="finalScore" class="text-purple-400 font-bold text-lg">0m</span>  
            </div>  
            <div class="flex justify-between items-center border-b border-purple-500/20 pb-2">  
                <span class="text-gray-400 text-sm">Max Level:</span>  
                <span id="finalLevel" class="text-cyan-400 font-bold text-lg">Level 1</span>  
            </div>  
            <div class="flex justify-between items-center">  
                <span class="text-gray-400 text-sm">Crystals Harvested:</span>  
                <span id="finalCrystals" class="text-pink-400 font-bold text-lg">0</span>  
            </div>  
        </div>  
  
        <!-- Reward Unlock Notice -->  
        <div id="rewardUnlockNotice" class="text-cyan-300 text-xs md:text-sm font-bold mb-6 text-center bg-cyan-950/60 border border-cyan-500/40 px-4 py-2 rounded-xl hidden">  
            🎁 Reward Unlocked: <span id="rewardTitle">Neon Nova Core</span>!  
        </div>  
  
        <button id="restartBtn" class="glow-button px-10 py-5 bg-gradient-to-r from-purple-600 to-pink-600 text-white font-black text-xl rounded-2xl uppercase tracking-wider cursor-pointer">  
            Re-Engage Drift  
        </button>  
    </div>  
  
    <canvas id="gameCanvas"></canvas>  
  
    <script>  
        const canvas = document.getElementById('gameCanvas');  
        const ctx = canvas.getContext('2d');  
  
        const startScreen = document.getElementById('startScreen');  
        const gameOverScreen = document.getElementById('gameOverScreen');  
        const startBtn = document.getElementById('startBtn');  
        const restartBtn = document.getElementById('restartBtn');  
        const scoreVal = document.getElementById('scoreVal');  
        const levelIndicator = document.getElementById('levelIndicator');  
        const crystalVal = document.getElementById('crystalVal');  
        const speedVal = document.getElementById('speedVal');  
        const finalScore = document.getElementById('finalScore');  
        const finalLevel = document.getElementById('finalLevel');  
        const finalCrystals = document.getElementById('finalCrystals');  
        const milestoneBanner = document.getElementById('milestoneBanner');  
        const milestoneText = document.getElementById('milestoneText');  
        const equippedSkin = document.getElementById('equippedSkin');  
        const skinBadge = document.getElementById('skinBadge');  
        const rewardUnlockNotice = document.getElementById('rewardUnlockNotice');  
        const rewardTitle = document.getElementById('rewardTitle');  
  
        let gameState = 'START'; // START, PLAYING, GAMEOVER  
        let distance = 0;  
        let crystals = 0;  
        let currentLevel = 1;  
          
        let baseGameSpeed = 3.2;  
        let gameSpeed = 3.2;  
        let maxGameSpeed = 15;  
        let speedMultiplier = 1.0;  
  
        let particles = [];  
        let obstacles = [];  
        let collectibles = [];  
  
        let unlockedSkins = ['Cyan Pulse Pod'];  
        let activeSkinIndex = 0;  
        const skinLibrary = [  
            { name: 'Cyan Pulse Pod', primary: '#06b6d4', core: '#ec4899', reqLevel: 1 },  
            { name: 'Emerald Vector Rig', primary: '#10b981', core: '#34d399', reqLevel: 2 },  
            { name: 'Solar Flare Core', primary: '#f59e0b', core: '#ef4444', reqLevel: 3 },  
            { name: 'Quantum Void Master', primary: '#8b5cf6', core: '#06b6d4', reqLevel: 4 }  
        ];  
  
        class Runner {  
            constructor() {  
                this.radius = 16;  
                this.reset();  
            }  
  
            reset() {  
                this.x = 120;  
                this.y = canvas.height * 0.75;  
                this.vy = 0;  
                this.gravity = 0.65;  
                this.isFlipped = false;  
                this.trail = [];  
            }  
  
            flip() {  
                this.isFlipped = !this.isFlipped;  
                this.gravity *= -1;  
                this.vy = this.isFlipped ? -7 : 7;  
  
                const skin = skinLibrary[activeSkinIndex];  
                for (let i = 0; i < 15; i++) {  
                    particles.push(new Particle(this.x, this.y, skin.core));  
                }  
            }  
  
            update() {  
                this.vy += this.gravity;  
                this.y += this.vy;  
  
                this.trail.push({ x: this.x, y: this.y });  
                if (this.trail.length > 12) this.trail.shift();  
  
                const ceilingY = canvas.height * 0.15 + this.radius;  
                const floorY = canvas.height * 0.85 - this.radius;  
  
                if (this.y > floorY) {  
                    this.y = floorY;  
                    this.vy = 0;  
                }  
                if (this.y < ceilingY) {  
                    this.y = ceilingY;  
                    this.vy = 0;  
                }  
            }  
  
            draw() {  
                const skin = skinLibrary[activeSkinIndex];  
  
                for (let i = 0; i < this.trail.length; i++) {  
                    const p = this.trail[i];  
                    const alpha = i / this.trail.length;  
                    ctx.beginPath();  
                    ctx.arc(p.x, p.y, this.radius * (i / this.trail.length), 0, Math.PI * 2);  
                    ctx.fillStyle = `rgba(168, 85, 247, ${alpha * 0.4})`;  
                    ctx.fill();  
                }  
  
                ctx.save();  
                ctx.translate(this.x, this.y);  
                ctx.beginPath();  
                ctx.arc(0, 0, this.radius, 0, Math.PI * 2);  
                ctx.fillStyle = '#0a061a';  
                ctx.fill();  
                ctx.lineWidth = 3;  
                ctx.strokeStyle = skin.primary;  
                ctx.shadowBlur = 15;  
                ctx.shadowColor = skin.primary;  
                ctx.stroke();  
  
                ctx.beginPath();  
                ctx.arc(0, 0, 6, 0, Math.PI * 2);  
                ctx.fillStyle = skin.core;  
                ctx.fill();  
                ctx.restore();  
            }  
        }  
  
        class Obstacle {  
            constructor(x) {  
                this.x = x;  
                this.width = 45;  
                this.height = (80 + Math.random() * 60) + Math.min(currentLevel * 10, 50);  
                this.isTop = Math.random() < 0.5;  
                this.y = this.isTop ? canvas.height * 0.15 : canvas.height * 0.85 - this.height;  
                this.passed = false;  
            }  
  
            update() {  
                this.x -= gameSpeed;  
            }  
  
            draw() {  
                ctx.save();  
                ctx.beginPath();  
                ctx.rect(this.x, this.y, this.width, this.height);  
                ctx.fillStyle = '#120c24';  
                ctx.fill();  
                ctx.lineWidth = 2;  
                ctx.strokeStyle = currentLevel >= 3 ? '#ef4444' : '#ec4899';  
                ctx.shadowBlur = 10;  
                ctx.shadowColor = ctx.strokeStyle;  
                ctx.stroke();  
                ctx.restore();  
            }  
        }  
  
        class Crystal {  
            constructor(x, y) {  
                this.x = x;  
                this.y = y;  
                this.size = 12;  
                this.angle = Math.random() * Math.PI;  
                this.collected = false;  
            }  
  
            update() {  
                this.x -= gameSpeed;  
                this.angle += 0.08;  
            }  
  
            draw() {  
                if (this.collected) return;  
                ctx.save();  
                ctx.translate(this.x, this.y + Math.sin(this.angle) * 8);  
                ctx.rotate(this.angle);  
                ctx.beginPath();  
                ctx.rect(-this.size/2, -this.size/2, this.size, this.size);  
                ctx.fillStyle = '#06b6d4';  
                ctx.shadowBlur = 12;  
                ctx.shadowColor = '#06b6d4';  
                ctx.fill();  
                ctx.restore();  
            }  
        }  
  
        class Particle {  
            constructor(x, y, color) {  
                this.x = x;  
                this.y = y;  
                const angle = Math.random() * Math.PI * 2;  
                const speed = Math.random() * 5 + 1;  
                this.vx = Math.cos(angle) * speed;  
                this.vy = Math.sin(angle) * speed;  
                this.radius = Math.random() * 3 + 1;  
                this.life = 35;  
                this.color = color;  
            }  
  
            update() {  
                this.x += this.vx - (gameSpeed * 0.5);  
                this.y += this.vy;  
                this.life--;  
            }  
  
            draw() {  
                ctx.beginPath();  
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);  
                ctx.fillStyle = this.color;  
                ctx.fill();  
            }  
        }  
  
        let runner = new Runner();  
  
        function resizeCanvas() {  
            canvas.width = window.innerWidth;  
            canvas.height = window.innerHeight;  
        }  
        window.addEventListener('resize', resizeCanvas);  
        resizeCanvas();  
  
        function triggerAction() {  
            if (gameState === 'PLAYING') {  
                runner.flip();  
            }  
        }  
  
        window.addEventListener('keydown', e => {  
            if (e.code === 'Space' || e.code === 'ArrowUp' || e.code === 'ArrowDown') {  
                e.preventDefault();  
                triggerAction();  
            }  
        });  
  
        window.addEventListener('pointerdown', e => {  
            if (gameState === 'PLAYING') {  
                triggerAction();  
            }  
        });  
  
        function showMilestone(text) {  
            milestoneText.innerText = text;  
            milestoneBanner.style.opacity = '1';  
            setTimeout(() => {  
                milestoneBanner.style.opacity = '0';  
            }, 2500);  
        }  
  
        function startGame() {  
            distance = 0;  
            crystals = 0;  
            currentLevel = 1;  
            gameSpeed = baseGameSpeed;  
            speedMultiplier = 1.0;  
            obstacles = [];  
            collectibles = [];  
            particles = [];  
            runner.reset();  
            scoreVal.innerText = '0m';  
            levelIndicator.innerText = 'LVL 1';  
            crystalVal.innerText = '0';  
            speedVal.innerText = '1.0x';  
            startScreen.classList.add('hidden');  
            gameOverScreen.classList.add('hidden');  
            rewardUnlockNotice.classList.add('hidden');  
            gameState = 'PLAYING';  
        }  
  
        startBtn.addEventListener('click', startGame);  
        restartBtn.addEventListener('click', startGame);  
  
        function triggerGameOver() {  
            gameState = 'GAMEOVER';  
            finalScore.innerText = Math.floor(distance) + 'm';  
            finalLevel.innerText = `Level ${currentLevel}`;  
            finalCrystals.innerText = crystals;  
  
            let newlyUnlocked = null;  
            if (currentLevel >= 2 && !unlockedSkins.includes('Emerald Vector Rig')) {  
                unlockedSkins.push('Emerald Vector Rig');  
                newlyUnlocked = 'Emerald Vector Rig';  
                activeSkinIndex = 1;  
            } else if (currentLevel >= 3 && !unlockedSkins.includes('Solar Flare Core')) {  
                unlockedSkins.push('Solar Flare Core');  
                newlyUnlocked = 'Solar Flare Core';  
                activeSkinIndex = 2;  
            } else if (currentLevel >= 4 && !unlockedSkins.includes('Quantum Void Master')) {  
                unlockedSkins.push('Quantum Void Master');  
                newlyUnlocked = 'Quantum Void Master';  
                activeSkinIndex = 3;  
            }  
  
            if (newlyUnlocked) {  
                rewardTitle.innerText = newlyUnlocked;  
                rewardUnlockNotice.classList.remove('hidden');  
            }  
  
            equippedSkin.innerText = skinLibrary[activeSkinIndex].name;  
            skinBadge.style.borderColor = skinLibrary[activeSkinIndex].primary;  
            skinBadge.style.boxShadow = `0 0 10px ${skinLibrary[activeSkinIndex].primary}`;  
  
            gameOverScreen.classList.remove('hidden');  
        }  
  
        function gameLoop() {  
            ctx.clearRect(0, 0, canvas.width, canvas.height);  
  
            const tunnelTop = canvas.height * 0.15;  
            const tunnelBottom = canvas.height * 0.85;  
  
            ctx.save();  
            ctx.lineWidth = 4;  
            ctx.strokeStyle = currentLevel >= 3 ? '#ef4444' : '#a855f7';  
            ctx.shadowBlur = 15;  
            ctx.shadowColor = ctx.strokeStyle;  
  
            ctx.beginPath();  
            ctx.moveTo(0, tunnelTop);  
            ctx.lineTo(canvas.width, tunnelTop);  
            ctx.stroke();  
  
            ctx.beginPath();  
            ctx.moveTo(0, tunnelBottom);  
            ctx.lineTo(canvas.width, tunnelBottom);  
            ctx.strokeStyle = '#06b6d4';  
            ctx.shadowColor = '#06b6d4';  
            ctx.stroke();  
            ctx.restore();  
  
            if (gameState === 'PLAYING') {  
                distance += gameSpeed * 0.1;  
                scoreVal.innerText = Math.floor(distance) + 'm';  
  
                let calculatedLevel = Math.floor(distance / 500) + 1;  
                if (calculatedLevel > currentLevel) {  
                    currentLevel = calculatedLevel;  
                    levelIndicator.innerText = `LVL ${currentLevel}`;  
                    showMilestone(`MILESTONE: LEVEL ${currentLevel} REACHED!`);  
                }  
  
                let targetSpeed = baseGameSpeed + (currentLevel - 1) * 0.8 + (distance % 500) * 0.002;  
                if (targetSpeed > maxGameSpeed) targetSpeed = maxGameSpeed;  
                gameSpeed = targetSpeed;  
                speedMultiplier = (gameSpeed / baseGameSpeed).toFixed(1);  
                speedVal.innerText = speedMultiplier + 'x';  
  
                let spawnInterval = Math.max(240 - currentLevel * 15, 140);  
                if (obstacles.length === 0 || obstacles[obstacles.length - 1].x < canvas.width - spawnInterval) {  
                    obstacles.push(new Obstacle(canvas.width));  
                    if (Math.random() < 0.75) {  
                        collectibles.push(new Crystal(canvas.width + 120, tunnelTop + 80 + Math.random() * (tunnelBottom - tunnelTop - 160)));  
                    }  
                }  
  
                for (let i = obstacles.length - 1; i >= 0; i--) {  
                    obstacles[i].update();  
                    obstacles[i].draw();  
  
                    const obs = obstacles[i];  
                    if (  
                        runner.x + runner.radius > obs.x &&  
                        runner.x - runner.radius < obs.x + obs.width &&  
                        runner.y + runner.radius > obs.y &&  
                        runner.y - runner.radius < obs.y + obs.height  
                    ) {  
                        for (let p = 0; p < 25; p++) {  
                            particles.push(new Particle(runner.x, runner.y, '#ec4899'));  
                        }  
                        triggerGameOver();  
                    }  
  
                    if (obs.x + obs.width < 0) {  
                        obstacles.splice(i, 1);  
                    }  
                }  
  
                for (let i = collectibles.length - 1; i >= 0; i--) {  
                    collectibles[i].update();  
                    collectibles[i].draw();  
  
                    const c = collectibles[i];  
                    const dist = Math.hypot(runner.x - c.x, runner.y - c.y);  
                    if (!c.collected && dist < runner.radius + c.size) {  
                        c.collected = true;  
                        crystals++;  
                        crystalVal.innerText = crystals;  
                        for (let p = 0; p < 10; p++) {  
                            particles.push(new Particle(c.x, c.y, '#06b6d4'));  
                        }  
                    }  
  
                    if (c.x < -50 || c.collected) {  
                        collectibles.splice(i, 1);  
                    }  
                }  
  
                runner.update();  
                runner.draw();  
            }  
  
            for (let i = particles.length - 1; i >= 0; i--) {  
                particles[i].update();  
                particles[i].draw();  
                if (particles[i].life <= 0) {  
                    particles.splice(i, 1);  
                }  
            }  
  
            requestAnimationFrame(gameLoop);  
        }  
  
        requestAnimationFrame(gameLoop);  
    </script>  
</body>  
</html>  
