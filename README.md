# anomaro18.github.io
%%html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>カードコレクションゲーム</title>
    <style>
        /* Global styles for the container */
        body {
            margin: 0;
            padding: 0;
            background-color: #1a1a1a; /* Dark background for the whole page */
        }

        div[style*="font-family"] { /* Targeting the main game container */
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            text-align: center;
            background-color: #282c34;
            color: #abb2bf;
            padding: 20px;
            border-radius: 8px;
            max-width: 900px;
            margin: 20px auto;
            box-shadow: 0 0 20px rgba(0,0,0,0.5);
        }

        /* Button Base Styles */
        button {
            padding: 10px 20px;
            border: none;
            border-radius: 4px;
            color: white;
            cursor: pointer;
            font-weight: bold;
            transition: background-color 0.3s ease;
            -webkit-tap-highlight-color: transparent; /* Remove tap highlight on iOS */
            touch-action: manipulation; /* Improve responsiveness for touch events */
        }

        /* Specific Button Styles */
        #drawPackButton {
            background-color: #61afef;
            margin-left: 10px;
        }
        #drawPackButton:hover {
            background-color: #3a8ee6;
        }

        #showEncyclopediaButton {
            background-color: #28a745;
            margin-left: 10px;
        }
        #showEncyclopediaButton:hover {
            background-color: #218838;
        }

        #backToPackButton {
            background-color: #f0ad4e;
            margin-top: 30px;
        }
        #backToPackButton:hover {
            background-color: #c98c37;
        }


        @keyframes pack-shake {
            0%, 100% { transform: translateX(0); }
            10%, 30%, 50%, 70%, 90% { transform: translateX(-5px); }
            20%, 40%, 60%, 80% { transform: translateX(5px); }
        }
        @keyframes pack-open-top-animation {
            0% { transform: rotateX(0deg) translateY(0px); opacity: 1; }
            20% { transform: rotateX(0deg) translateY(-5px); } /* Slight lift */
            100% { transform: rotateX(-90deg) translateY(-50px) translateZ(50px); opacity: 0; }
        }
        @keyframes pack-open-text-fade-in-out {
            0% { opacity: 0; transform: translate(-50%, -50%) scale(0.8); }
            50% { opacity: 1; transform: translate(-50%, -50%) scale(1.1); text-shadow: 0 0 10px #61afef, 0 0 20px #61afef; } /* Scale and glow */
            100% { opacity: 0; transform: translate(-50%, -50%) scale(1.2); }
        }

        /* Updated card entry animation */
        @keyframes card-fall-rotate-in {
            0% {
                transform: translateY(-150px) rotateY(180deg) scale(0.7);
                opacity: 0;
            }
            50% {
                opacity: 1;
                transform: translateY(-20px) rotateY(90deg) scale(0.9);
            }
            100% {
                transform: translateY(0px) rotateY(0deg) scale(1);
                opacity: 1;
            }
        }

        .card {
            width: 180px;
            height: 280px; /* Slightly taller to accommodate the frame */
            border-radius: 12px; /* Slightly more rounded */
            box-shadow: 0 6px 20px rgba(0,0,0,0.6); /* Stronger shadow */
            display: flex;
            flex-direction: column; /* Organize content vertically */
            /* justify-content: space-between; Removed to allow header to be fixed top */
            align-items: center;
            padding: 0; /* Remove padding from main card, move to inner elements */
            box-sizing: border-box;
            text-align: center;
            font-size: 0.9em;
            color: white;
            position: relative;
            overflow: hidden; /* Important for inner elements not to spill */
            animation: card-fall-rotate-in 1.0s ease-out forwards; /* Apply new animation */
            transform-style: preserve-3d;
            backface-visibility: hidden;
            transition: transform 0.3s ease-in-out, box-shadow 0.3s ease;
        }
        .card:hover {
            transform: translateY(-8px) scale(1.03); /* More pronounced hover effect */
            box-shadow: 0 10px 30px rgba(0,0,0,0.7);
        }

        .card-header-frame {
            width: 100%;
            padding: 10px 5px;
            background-color: rgba(0, 0, 0, 0.3); /* Semi-transparent dark background */
            border-bottom: 2px solid rgba(255, 255, 255, 0.2); /* Subtle separator */
            box-sizing: border-box;
            border-top-left-radius: 10px;
            border-top-right-radius: 10px;
            z-index: 1; /* Ensure it stays on top */
        }

        .card-name {
            font-size: 1.4em; /* Larger font for name */
            font-weight: bold;
            text-shadow: 1px 1px 3px rgba(0,0,0,0.7); /* Text shadow for better readability */
            margin-bottom: 0; /* No margin below name within frame */
            color: #f0f0f0; /* Slightly off-white for name */
        }

        .card-content {
            flex-grow: 1; /* Allow content to take remaining space */
            display: flex;
            flex-direction: column;
            justify-content: space-around; /* Distribute content nicely */
            padding: 10px 15px 15px; /* Padding for the main content area */
            width: 100%;
            box-sizing: border-box;
        }

        .card-rarity { font-size: 1.1em; font-weight: bold; margin-bottom: 8px; color: #aaffa0; } /* Greenish tint for rarity */
        .card-stats { margin-bottom: 12px; line-height: 1.4; }
        .card-stats div {
            font-size: 0.95em;
            margin-bottom: 2px;
        }
        .card-skill { font-style: italic; font-size: 0.9em; line-height: 1.3; color: #b2ccff; } /* Blueish tint for skill */
        .card-skill div {
            margin-bottom: 2px;
        }

        /* Rarity based colors (updated for 50-point intervals and new ranges) */
        .rarity-1-50 { background: linear-gradient(135deg, #4b4b4b, #2c2c2c); border: 2px solid #707070; } /* 劣悪 (Inferior) */
        .rarity-51-100 { background: linear-gradient(135deg, #5a5a5a, #3c3c3c); border: 2px solid #808080; } /* 劣悪+ (Inferior+) */
        .rarity-101-150 { background: linear-gradient(135deg, #6a6a6a, #4c4c4c); border: 2px solid #909090; } /* 一般 (Normal) */
        .rarity-151-200 { background: linear-gradient(135deg, #7a522a, #4a301a); border: 2px solid #b37e4c; } /* 並品 (Fair) */
        .rarity-201-250 { background: linear-gradient(135deg, #8b633b, #5b412b); border: 2px solid #c48f5d; } /* 良品 (Good) */
        .rarity-251-300 { background: linear-gradient(135deg, #9c744c, #6c523c); border: 2px solid #d5a06e; } /* 優良 (Superior) */
        .rarity-301-350 { background: linear-gradient(135deg, #3c6e71, #2c5154); border: 2px solid #5fa6a9; } /* 稀少 (Rare) */
        .rarity-351-400 { background: linear-gradient(135deg, #4c7e81, #3c6164); border: 2px solid #6fb7ba; } /* 稀少+ (Rare+) */
        .rarity-401-450 { background: linear-gradient(135deg, #5c4b8b, #3c305a); border: 2px solid #8e7ec2; } /* 聖級 (Holy) */
        .rarity-451-500 { background: linear-gradient(135deg, #6d5c9c, #4d416b); border: 2px solid #9f8fd3; } /* 聖級+ (Holy+) */
        .rarity-501-550 { background: linear-gradient(135deg, #d4af37, #a84200); border: 2px solid #f0d57b; } /* 伝説 (Legendary) */
        .rarity-551-600 { background: linear-gradient(135deg, #e5c048, #b9973d); border: 2px solid #ffe68c; } /* 伝説+ (Legendary+) */
        .rarity-601-650 { background: linear-gradient(135deg, #a83d3d, #7c2d2d); border: 2px solid #d45e5e; } /* 神話 (Mythic) */
        .rarity-651-700 { background: linear-gradient(135deg, #b94e4e, #8d3e3e); border: 2px solid #e56f6f; } /* 神話+ (Mythic+) */
        .rarity-701-750 { background: linear-gradient(135deg, #4a148c, #2a0c4f); border: 2px solid #880e4f; } /* 天界 (Celestial) */
        .rarity-751-800 { background: linear-gradient(135deg, #5b259d, #3b1d60); border: 2px solid #991f60; } /* 天界+ (Celestial+) */
        .rarity-801-850 { background: linear-gradient(135deg, #e75c0e, #a84200); border: 2px solid #ff7b30; } /* 究極 (Ultimate) */
        .rarity-851-900 { background: linear-gradient(135deg, #f86d1f, #b95311); border: 2px solid #ff8c41; } /* 究極+ (Ultimate+) */
        .rarity-901-950 { background: linear-gradient(135deg, #00ced1, #008b8b); border: 2px solid #48d1cc; } /* 超越 (Transcendent) */
        .rarity-951-999 { background: linear-gradient(135deg, #11dfd2, #119c9c); border: 2px solid #59e2dd; } /* 至高 (Supreme) */

        .rarity-group-header {
            width: 100%;
            text-align: left;
            font-size: 1.8em;
            font-weight: bold;
            color: #e0e0e0;
            margin-top: 30px;
            margin-bottom: 15px;
            padding-bottom: 5px;
            border-bottom: 2px solid #61afef;
        }

        /* Tab Styles */
        .rarity-tab-button {
            padding: 8px 15px;
            border: 1px solid #61afef;
            border-radius: 5px;
            background-color: #3e4451;
            color: #abb2bf;
            cursor: pointer;
            transition: background-color 0.2s ease, color 0.2s ease;
            font-weight: bold;
        }
        .rarity-tab-button:hover {
            background-color: #61afef;
            color: white;
        }
        .rarity-tab-button.active {
            background-color: #61afef;
            color: white;
            border-color: #fff;
        }
        .rarity-group-container {
            display: none; /* Hidden by default, shown by JS */
            flex-wrap: wrap;
            justify-content: center;
            gap: 20px;
            padding-top: 10px;
        }
        .rarity-group-container.active {
            display: flex;
        }
        .rarity-group-header-in-tab {
            width: 100%;
            text-align: left;
            font-size: 1.5em; /* Slightly smaller for tab content */
            font-weight: bold;
            color: #e0e0e0;
            margin-bottom: 15px;
            padding-bottom: 5px;
            border-bottom: 1px solid #61afef;
        }

        /* General Shine Effect */
        @keyframes shine-animation {
            0% { background-position: -200% 0; }
            100% { background-position: 200% 0; }
        }

        .card.has-shine {
            position: relative;
            overflow: hidden; /* Ensure shine doesn't spill */
        }

        .card.has-shine::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle at center, rgba(255,255,255,0.1) 0%, rgba(255,255,255,0) 70%);
            transform: rotate(45deg);
            opacity: 0;
            transition: opacity 0.5s ease;
            pointer-events: none; /* Allow interaction with card below */
            z-index: 2; /* Above card content, below holographic overlay if any */
        }

        .card.has-shine:hover::before {
            opacity: 1; /* Shine appears on hover */
        }

        /* Holographic effect */
        .card.has-holographic {
            position: relative;
        }

        .card.has-holographic::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            border-radius: 12px;
            background: linear-gradient(
                -45deg,
                rgba(255, 255, 255, 0) 20%,
                rgba(255, 255, 255, 0.1) 25%,
                rgba(255, 255, 255, 0.2) 30%,
                rgba(255, 255, 255, 0.1) 35%,
                rgba(255, 255, 255, 0) 40%
            );
            background-size: 200% 200%;
            animation: holographic-animation 5s linear infinite;
            opacity: 0.8; /* Make it subtle */
            pointer-events: none;
            z-index: 3; /* Above shine and content */
        }

        @keyframes holographic-animation {
            0% { background-position: 0% 0%; }
            100% { background-position: 100% 100%; }
        }

        /* NEW Mark Style */
        .new-mark {
            position: absolute;
            top: 10px;
            right: 10px;
            background-color: #e74c3c; /* Red color for NEW */
            color: white;
            padding: 4px 8px;
            border-radius: 5px;
            font-weight: bold;
            font-size: 0.75em;
            z-index: 5;
            box-shadow: 0 2px 5px rgba(0,0,0,0.3);
            transform: rotate(10deg);
            animation: new-mark-bounce 1s infinite alternate;
        }

        @keyframes new-mark-bounce {
            0% { transform: scale(1) rotate(10deg); }
            100% { transform: scale(1.05) rotate(10deg); }
        }

    </style>
</head>
<body>
    <div style="font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; text-align: center; background-color: #282c34; color: #abb2bf; padding: 20px; border-radius: 8px; max-width: 900px; margin: 20px auto; box-shadow: 0 0 20px rgba(0,0,0,0.5);">
        <h1>カードコレクションゲーム</h1>
        <p>あなたの名前を入力して、カードパックを引いてみましょう！</p>
        <div style="margin-bottom: 20px;">
            <label for="playerName" style="margin-right: 10px; font-weight: bold;">名前:</label>
            <input type="text" id="playerName" value="プレイヤー1" style="padding: 8px; border-radius: 4px; border: 1px solid #61afef; background-color: #3e4451; color: #abb2bf; width: 200px;">
            <button id="drawPackButton">パックを引く</button>
            <button id="showEncyclopediaButton">図鑑</button>
        </div>

        <div class="game-container" style="display: flex; flex-direction: column; align-items: center;">
            <div class="pack-card-display-wrapper">
                <div class="card-pack-area" style="position: relative; width: 200px; height: 300px; margin-bottom: 30px; perspective: 1000px; ">
                    <div id="cardPack" class="card-pack" style="position: absolute; width: 100%; height: 100%; border-radius: 10px; box-shadow: 0 4px 8px rgba(0,0,0,0.3); transform-style: preserve-3d; transition: transform 0.5s ease; backface-visibility: hidden;">
                        <div class="pack-body" style="position: absolute; width: 100%; height: 100%; background-color: #a366ff; border-radius: 10px; background: linear-gradient(135deg, #a366ff, #6133a3); border: 2px solid #b886ff; display: flex; justify-content: center; align-items: center; color: white; font-size: 2em; font-weight: bold; text-shadow: 2px 2px 4px rgba(0,0,0,0.5);">PACK</div>
                        <div class="pack-top" style="position: absolute; top: 0; left: 0; width: 100%; height: 20%; background-color: #c09bff; border-top-left-radius: 10px; border-top-right-radius: 10px; background: linear-gradient(135deg, #c09bff, #8a57cf); border-bottom: 2px solid #b886ff; transform-origin: top center; transition: transform 0.5s ease-out, opacity 0.5s ease; z-index: 10;"></div>
                    </div>
                    <div id="packOpenText" class="pack-open-text" style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); color: #61afef; font-size: 1.8em; font-weight: bold; text-shadow: 2px 2px 4px rgba(0,0,0,0.8); opacity: 0; transition: opacity 0.5s ease; z-index: 20; white-space: nowrap;">パックオープン！</div>
                </div>
                <div id="cardDisplayArea" class="card-display-area" style="display: flex; flex-wrap: wrap; justify-content: center; gap: 20px; min-height: 400px; margin-top: 20px;">
                    <!-- Cards will be displayed here -->
                </div>
            </div>

            <div id="encyclopediaArea" class="encyclopedia-area" style="display: none; width: 100%; max-height: 700px; overflow-y: auto; padding: 20px; border-radius: 8px; background-color: #3e4451; box-shadow: inset 0 0 10px rgba(0,0,0,0.3);">
                <h2 style="color: #61afef; margin-bottom: 20px;">カード図鑑</h2>
                <div class="rarity-tabs" style="display: flex; flex-wrap: wrap; justify-content: center; gap: 10px; margin-bottom: 20px;">
                    <!-- Rarity tab buttons will go here -->
                </div>
                <div id="encyclopediaContent" style="max-height: calc(700px - 150px); overflow-y: auto;">
                    <!-- Rarity group containers will go here -->
                </div>
                <button id="backToPackButton">パックを引く画面に戻る</button>
            </div>
        </div>
    </div>

    <script>
        const playerNameInput = document.getElementById('playerName');
        const drawPackButton = document.getElementById('drawPackButton');
        const showEncyclopediaButton = document.getElementById('showEncyclopediaButton');
        const backToPackButton = document.getElementById('backToPackButton');
        const cardPack = document.getElementById('cardPack');
        const packTop = cardPack.querySelector('.pack-top');
        const packBody = cardPack.querySelector('.pack-body');
        const packOpenText = document.getElementById('packOpenText');
        const cardDisplayArea = document.getElementById('cardDisplayArea');
        const encyclopediaArea = document.getElementById('encyclopediaArea');
        const encyclopediaContent = document.getElementById('encyclopediaContent'); // New: Container for rarity groups
        const rarityTabsContainer = document.querySelector('.rarity-tabs'); // New: Container for tab buttons
        const packCardDisplayWrapper = document.querySelector('.pack-card-display-wrapper');

        let collectedCards = []; // Array to store all generated cards

        // Function to save collected cards to localStorage
        function saveCards() {
            localStorage.setItem('collectedCards', JSON.stringify(collectedCards));
        }

        // Function to load collected cards from localStorage
        function loadCards() {
            const savedCards = localStorage.getItem('collectedCards');
            if (savedCards) {
                collectedCards = JSON.parse(savedCards);
            } else {
                collectedCards = [];
            }
        }

        // Load cards on script initialization
        loadCards();

        // Seeded Random Number Generator for consistent results
        function mulberry32(seed) {
            return function() {
                seed |= 0;
                seed = (seed + 0x6D2B79F5) | 0;
                let t = Math.imul(seed ^ (seed >>> 15), 1 | seed);
                t = (t + Math.imul(t ^ (t >>> 13), 1 | t)) | 0;
                return (t >>> 0) / 0x100000000; // Corrected: Normalize to [0, 1) using full 32-bit value
            };
        }

        // Simple string hash function to generate a seed
        function stringToSeed(str) {
            let hash = 0;
            for (let i = 0; i < str.length; i++) {
                hash = str.charCodeAt(i) + ((hash << 5) - hash);
            }
            return hash;
        }

        function generateCardData(playerName, cardIndex, seed) {
            const rng = mulberry32(seed); // A new RNG instance for each card
            const rawRngValues = {}; // To store raw RNG values for debugging

            // --- Rarity generation (fixed RNG consumption, skewed towards lower rarities) ---
            let rarity;

            // Always consume random numbers for all potential rarity paths to fix RNG sequence
            // This ensures the number of rng() calls is consistent, making subsequent stats truly random for different seeds
            let val1 = rng(); rawRngValues.val1 = val1;
            let val2 = rng(); rawRngValues.val2 = val2;
            let val3 = rng(); rawRngValues.val3 = val3;
            let val4 = rng(); rawRngValues.val4 = val4;
            let val5 = rng(); rawRngValues.val5 = val5;
            let val6 = rng(); rawRngValues.val6 = val6;
            let val7 = rng(); rawRngValues.val7 = val7;

            if (val1 < 0.6) { // 60% chance to skew towards lower rarities
                let sumRng = (val2 + val3 + val4 + val5 + val6) / 5; // Average of 5 calls
                // Adjusted range: make it broader to accommodate new rarity ranges and skew lower
                rarity = Math.floor(sumRng * 400) + 100; // Scaled to be centered around 300 (range ~100-500)
            } else { // 40% chance for wider, more uniform distribution (1-999)
                rarity = Math.floor(val7 * 999) + 1;
            }

            rarity = Math.max(1, Math.min(999, rarity)); // Ensure within 1-999

            // --- Stats generation (consistent RNG calls) ---
            let baseStats = 10 + Math.floor(rarity / 50) * 2; // Higher rarity, higher base
            const rngAttack = rng(); rawRngValues.rngAttack = rngAttack;
            const attack = baseStats + Math.floor(rngAttack * baseStats * 0.5); // uses next rng value
            const rngDefense = rng(); rawRngValues.rngDefense = rngDefense;
            const defense = baseStats + Math.floor(rngDefense * baseStats * 0.4); // uses next rng value
            const rngHp = rng(); rawRngValues.rngHp = rngHp;
            const hp = baseStats * 2 + Math.floor(rngHp * baseStats); // uses next rng value

            // --- Skill generation (consistent RNG calls) ---
            const skillTypes = [
                { name: "スラッシュ", effect: "物理攻撃", basePower: 20 },
                { name: "ファイアボール", effect: "火属性攻撃", basePower: 30 },
                { name: "ヒール", effect: "HP回復", basePower: 25 },
                { name: "ポイズンアタック", effect: "毒付与", basePower: 15 },
                { name: "シールド", effect: "防御力アップ", basePower: 20 },
                { name: "ライトニング", effect: "雷属性攻撃", basePower: 35 },
                { name: "ドレイン", effect: "HP吸収", basePower: 18 }
            ];

            const rngSkillIndex = rng(); rawRngValues.rngSkillIndex = rngSkillIndex;
            let skillIndex = Math.floor(rngSkillIndex * skillTypes.length); // uses next rng value
            const selectedSkill = skillTypes[skillIndex];

            let skillName = selectedSkill.name;
            let skillEffect = selectedSkill.effect;
            // Skill power also depends on rarity and a dedicated rng call for variability
            const rngSkillPower = rng(); rawRngValues.rngSkillPower = rngSkillPower;
            let skillPower = selectedSkill.basePower + Math.floor(rarity / 100) * 5 + Math.floor(rngSkillPower * 10); // uses next rng value


            // Card color and effects based on rarity (updated logic for 50-point intervals)
            let rarityClass;
            let effectClasses = ''; // New variable for effects

            if (rarity <= 50) { rarityClass = 'rarity-1-50'; } // 劣悪
            else if (rarity <= 100) { rarityClass = 'rarity-51-100'; } // 劣悪+
            else if (rarity <= 150) { rarityClass = 'rarity-101-150'; } // 一般
            else if (rarity <= 200) { rarityClass = 'rarity-151-200'; } // 並品
            else if (rarity <= 250) { rarityClass = 'rarity-201-250'; } // 良品
            else if (rarity <= 300) { rarityClass = 'rarity-251-300'; } // 優良
            else if (rarity <= 350) { rarityClass = 'rarity-301-350'; } // 稀少
            else if (rarity <= 400) { rarityClass = 'rarity-351-400'; } // 稀少+
            else if (rarity <= 450) { rarityClass = 'rarity-401-450'; } // 聖級
            else if (rarity <= 500) { rarityClass = 'rarity-451-500'; } // 聖級+
            else if (rarity <= 550) { rarityClass = 'rarity-501-550'; } // 伝説
            else if (rarity <= 600) { rarityClass = 'rarity-551-600'; } // 伝説+
            else if (rarity <= 650) { rarityClass = 'rarity-601-650'; } // 神話
            else if (rarity <= 700) { rarityClass = 'rarity-651-700'; } // 神話+
            else if (rarity <= 750) { rarityClass = 'rarity-701-750'; } // 天界
            else if (rarity <= 800) { rarityClass = 'rarity-751-800'; } // 天界+
            else if (rarity <= 850) { rarityClass = 'rarity-801-850'; } // 究極
            else if (rarity <= 900) { rarityClass = 'rarity-851-900'; } // 究極+
            else if (rarity <= 950) { rarityClass = 'rarity-901-950'; } // 超越
            else { rarityClass = 'rarity-951-999'; } // 至高

            if (rarity >= 300) { effectClasses = 'has-shine'; }
            if (rarity >= 851) { effectClasses += ' has-holographic'; } // Holographic only for 851+ (Celestial and above)

            const cardIdentifier = JSON.stringify({
                playerName: playerName,
                rarity: rarity,
                stats: { attack, defense, hp },
                skill: { name: skillName, power: skillPower, effect: skillEffect }
            });

            return {
                id: stringToSeed(cardIdentifier), // Use a derived ID for uniqueness
                playerName: playerName,
                rarity: rarity,
                rarityClass: rarityClass,
                effectClasses: effectClasses, // Pass effect classes
                stats: { attack, defense, hp },
                skill: { name: skillName, power: skillPower, effect: skillEffect },
                rawRngValues: rawRngValues, // Add raw RNG values for debugging
                isNew: true // Set isNew flag to true for newly generated cards
            };
        }

        function createCardElement(cardData, delay) {
            const cardDiv = document.createElement('div');
            cardDiv.className = `card ${cardData.rarityClass} ${cardData.effectClasses}`;
            if (delay !== undefined) {
                cardDiv.style.animationDelay = `${delay}s`;
            }

            let newMarkHtml = '';
            if (cardData.isNew) {
                newMarkHtml = '<div class="new-mark">NEW</div>';
            }

            cardDiv.innerHTML = `
                ${newMarkHtml}
                <div class="card-header-frame">
                    <div class="card-name">${cardData.playerName}</div>
                </div>
                <div class="card-content">
                    <div class="card-rarity">レアリティ: ${cardData.rarity}</div>
                    <div class="card-stats">
                        <div>攻撃: ${cardData.stats.attack}</div>
                        <div>防御: ${cardData.stats.defense}</div>
                        <div>HP: ${cardData.stats.hp}</div>
                    </div>
                    <div class="card-skill">
                        <div>技: ${cardData.skill.name}</div>
                        <div>威力: ${cardData.skill.power}</div>
                        <div>効果: ${cardData.skill.effect}</div>
                    </div>
                </div>
            `;
            return cardDiv;
        }

        // Helper to get rarity group name based on rarity score
        function getRarityGroupName(rarity) {
            if (rarity <= 50) return 'コモン (1-50)';
            else if (rarity <= 100) return 'コモン+ (51-100)';
            else if (rarity <= 150) return 'アンコモン (101-150)';
            else if (rarity <= 200) return 'アンコモン+ (151-200)';
            else if (rarity <= 250) return 'レア (201-250)';
            else if (rarity <= 300) return 'レア+ (251-300)';
            else if (rarity <= 350) return 'スーパーレア (301-350)';
            else if (rarity <= 400) return 'スーパーレア+ (351-400)';
            else if (rarity <= 450) return 'エピック (401-450)';
            else if (rarity <= 500) return 'エピック+ (451-500)';
            else if (rarity <= 550) return 'レジェンダリー (501-550)';
            else if (rarity <= 600) return 'レジェンダリー+ (551-600)';
            else if (rarity <= 650) return 'ミシック (601-650)';
            else if (rarity <= 700) return 'ミシック+ (651-700)';
            else if (rarity <= 750) return 'セレスティアル (701-750)';
            else if (rarity <= 800) return 'セレスティアル+ (751-800)';
            else if (rarity <= 850) return 'アルティメット (801-850)';
            else if (rarity <= 900) return 'アルティメット+ (851-900)';
            else if (rarity <= 950) return 'トランセンデント (901-950)';
            else return 'スプリーム (951-999)';
        }

        function renderEncyclopedia() {
            console.log('Rendering Encyclopedia...');
            let cardsUpdated = false;
            for (let i = 0; i < collectedCards.length; i++) {
                if (collectedCards[i].isNew) {
                    collectedCards[i].isNew = false;
                    cardsUpdated = true;
                }
            }

            if (cardsUpdated) {
                saveCards(); // Save updated collectedCards array to localStorage
            }

            encyclopediaContent.innerHTML = ''; // Clear previous content containers
            rarityTabsContainer.innerHTML = ''; // Clear previous tab buttons

            if (collectedCards.length === 0) {
                const noCardsMessage = document.createElement('p');
                noCardsMessage.textContent = 'まだカードがありません。パックを引いてみましょう！';
                noCardsMessage.style.color = '#ccc';
                encyclopediaContent.appendChild(noCardsMessage);
                return;
            }

            // Step 1: Sort all collected cards by rarity in descending order.
            // This ensures that when cards are grouped, they are already sorted within each group.
            const sortedCards = [...collectedCards].sort((a, b) => b.rarity - a.rarity);

            const cardsByRarityGroup = {};
            const rarityGroupOrder = []; // To maintain a consistent order of tabs

            for (const cardData of sortedCards) {
                const groupName = getRarityGroupName(cardData.rarity);
                if (!cardsByRarityGroup[groupName]) {
                    cardsByRarityGroup[groupName] = [];
                    rarityGroupOrder.push(groupName);
                }
                cardsByRarityGroup[groupName].push(cardData); // Cards are pushed in already sorted order
            }

            // Step 2: Sort rarity group names (tabs) by their minimum rarity value in descending order.
            // This ensures tabs are displayed from highest rarity range (e.g., Transcendent) to lowest (e.g., Common).
            rarityGroupOrder.sort((a, b) => {
                // Custom sort order based on rarity ranges (descending)
                const getMinRarity = (name) => parseInt(name.match(/\((\d+)-/)[1]);
                return getMinRarity(b) - getMinRarity(a);
            });

            let firstTabButton = null;

            for (const groupName of rarityGroupOrder) {
                // Create tab button
                const tabButton = document.createElement('button');
                tabButton.textContent = groupName;
                tabButton.className = 'rarity-tab-button';
                tabButton.dataset.group = groupName; // Store group name for easy access
                rarityTabsContainer.appendChild(tabButton);
                if (!firstTabButton) { firstTabButton = tabButton; }

                // Create content container for this rarity group
                const groupContainer = document.createElement('div');
                groupContainer.id = `rarity-group-${groupName.replace(/[^a-zA-Z0-9]/g, '-')}`; // Sanitize ID
                groupContainer.className = 'rarity-group-container';

                // Add a header inside the tab content for clarity
                const headerInTab = document.createElement('h4');
                headerInTab.className = 'rarity-group-header-in-tab';
                headerInTab.textContent = groupName + ' カード一覧';
                groupContainer.appendChild(headerInTab);

                const cardsWrapper = document.createElement('div');
                cardsWrapper.style.display = 'flex';
                cardsWrapper.style.flexWrap = 'wrap';
                cardsWrapper.style.justifyContent = 'center';
                cardsWrapper.style.gap = '20px';
                groupContainer.appendChild(cardsWrapper);

                for (const cardData of cardsByRarityGroup[groupName]) {
                    cardsWrapper.appendChild(createCardElement(cardData));
                }
                encyclopediaContent.appendChild(groupContainer);

                // Add event listener to tab button
                tabButton.addEventListener('click', () => {
                    console.log(`Tab button clicked: ${groupName}`);
                    // Deactivate all tab buttons and hide all group containers
                    document.querySelectorAll('.rarity-tab-button').forEach(btn => btn.classList.remove('active'));
                    document.querySelectorAll('.rarity-group-container').forEach(container => container.classList.remove('active'));

                    // Activate clicked tab button and show its corresponding group container
                    tabButton.classList.add('active');
                    groupContainer.classList.add('active');
                });
            }

            // Activate the first tab by default
            if (firstTabButton) {
                firstTabButton.click();
            }
        }

        drawPackButton.addEventListener('click', () => {
            console.log('Draw Pack button clicked!');
            // Hide encyclopedia if it's currently visible
            packCardDisplayWrapper.style.display = 'flex';
            encyclopediaArea.style.display = 'none';

            // --- DEBUG LOGS START ---
            console.log('--- Draw Pack Clicked ---');
            console.log('Input Field Value:', playerNameInput.value); // Log raw input field value
            // --- DEBUG LOGS END ---

            const playerName = playerNameInput.value.trim();
            if (!playerName) {
                alert('名前を入力してください！');
                return;
            }

            // --- DEBUG LOGS START ---
            console.log('Input Player Name:', playerName);
            const baseSeed = stringToSeed(playerName);
            console.log('Generated Base Seed:', baseSeed);
            // --- DEBUG LOGS END ---

            cardDisplayArea.innerHTML = ''; // Clear previous cards
            packBody.style.opacity = '1';
            packTop.style.transform = 'rotateX(0deg)';
            packTop.style.opacity = '1';
            packTop.style.display = 'block'; // Ensure pack top is visible
            cardPack.style.display = 'block'; // Show pack if hidden
            packOpenText.style.opacity = '0'; // Hide text initially

            // Reset animations
            packTop.style.animation = 'none';
            packOpenText.style.animation = 'none';
            cardPack.style.animation = 'none'; // Reset shake animation
            void packTop.offsetWidth; // Trigger reflow
            void packOpenText.offsetWidth; // Trigger reflow
            void cardPack.offsetWidth; // Trigger reflow

            // Shake animation
            cardPack.style.animation = 'pack-shake 0.3s ease-in-out';

            setTimeout(() => {
                cardPack.style.animation = 'none'; // Remove shake animation after it plays
                // Pack opening animation
                packTop.style.animation = 'pack-open-top-animation 0.8s forwards';
                packOpenText.style.animation = 'pack-open-text-fade-in-out 1.5s forwards';
            }, 300); // After shake animation finishes

            setTimeout(() => {
                // After pack top animation
                packTop.style.display = 'none';
                packBody.style.opacity = '0'; // Hide pack body
                cardPack.style.display = 'none'; // Hide the entire card pack element
            }, 1200);

            setTimeout(() => {
                const numCards = 1; // Draw only 1 card per pack

                for (let i = 0; i < numCards; i++) {
                    const cardSeed = baseSeed + i;
                    let generatedCardData = generateCardData(playerName, i, cardSeed); // This card, fresh from the pack, has isNew: true

                    // Check if this card (identified by its unique ID) already exists in the collectedCards
                    const existingCardInCollection = collectedCards.find(c => c.id === generatedCardData.id);

                    if (!existingCardInCollection) {
                        // Card is truly new to the collection
                        collectedCards.push(generatedCardData); // Add with isNew: true (from generateCardData)
                        // The generatedCardData for display will also correctly have isNew: true
                    } else {
                        // Card is a duplicate.
                        // For display purposes, the *drawn* duplicate card should NOT show 'NEW'.
                        generatedCardData.isNew = false; // Set isNew to false for this *displayed* card only
                        // The existing card in collectedCards keeps its current isNew status. We don't modify it here.
                    }
                    saveCards(); // Save cards after adding/updating collectedCards

                    // --- DEBUG LOGS START ---
                    console.log('Generated Card Data (for display):', JSON.stringify(generatedCardData, null, 2)); // Detailed log
                    // --- DEBUG LOGS END ---

                    // The cardElement is created using `generatedCardData`, which now correctly reflects
                    // whether the *drawn card* should show 'NEW' (true for new unique card, false for duplicate).
                    const cardElement = createCardElement(generatedCardData, i * 0.2);
                    cardDisplayArea.appendChild(cardElement);
                }
            }, 1500); // Start displaying cards after pack animation
        });

        showEncyclopediaButton.addEventListener('click', () => {
            console.log('Show Encyclopedia button clicked!');
            packCardDisplayWrapper.style.display = 'none';
            encyclopediaArea.style.display = 'block';
            renderEncyclopedia();
        });

        backToPackButton.addEventListener('click', () => {
            console.log('Back to Pack button clicked!');
            encyclopediaArea.style.display = 'none';
            packCardDisplayWrapper.style.display = 'flex';
            // Ensure the pack area is visible when returning to pack view
            cardPack.style.display = 'block';
        });

        // Initial pack state on load
        packTop.style.animation = 'none';
        packOpenText.style.animation = 'none';
        packOpenText.style.opacity = '0';
    </script>
</body>
</html>
