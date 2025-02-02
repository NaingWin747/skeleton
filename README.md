<!DOCTYPE html>
<html>
<head>
    <title>RedDevil Cat House 😼</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style>
        :root {
            --main-color: #ff6b6b;
            --bg-color: #fff5f5;
        }
        body {
            font-family: 'Arial Rounded', Arial, sans-serif;
            background: var(--bg-color);
            margin: 0;
            padding: 15px;
        }
        .header {
            text-align: center;
            padding: 20px;
        }
        .cat-container {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
            margin-top: 20px;
        }
        .cat-card {
            background: white;
            border-radius: 20px;
            padding: 15px;
            text-align: center;
            box-shadow: 0 3px 6px rgba(0,0,0,0.1);
        }
        .cat-image {
            width: 100px;
            height: 100px;
            margin: 0 auto;
            background: #ffe3e3;
            border-radius: 15px;
        }
        .button {
            background: var(--main-color);
            color: white;
            padding: 10px 20px;
            border-radius: 10px;
            border: none;
            margin: 5px;
            cursor: pointer;
        }
        .currency {
            position: fixed;
            top: 10px;
            right: 10px;
            background: white;
            padding: 8px 15px;
            border-radius: 20px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
    </style>
</head>
<body>
    <div class="currency">🐟 Fish: <span id="fishCount">0</span></div>
    <div class="header">
        <h1>😼 RedDevil Cat House</h1>
        <button class="button" onclick="collectFish()">🎣 Collect Fish</button>
        <button class="button" onclick="openShop()">🛍️ Shop</button>
    </div>

    <div class="cat-container" id="catContainer">
        <!-- Cats will be added here dynamically -->
    </div>

    <script>
        const tg = Telegram.WebApp;
        tg.ready();
        tg.expand();

        // Game State
        let state = {
            fish: localStorage.getItem('fish') || 0,
            cats: JSON.parse(localStorage.getItem('cats')) || [
                { id: 1, type: 'normal', level: 1 }
            ]
        };

        // Initialize
        function init() {
            updateFishDisplay();
            renderCats();
        }

        function renderCats() {
            const container = document.getElementById('catContainer');
            container.innerHTML = state.cats.map(cat => `
                <div class="cat-card">
                    <div class="cat-image">${getCatEmoji(cat.type)}</div>
                    <h3>${cat.type.charAt(0).toUpperCase() + cat.type.slice(1)} Cat</h3>
                    <p>Level: ${cat.level}</p>
                    <button class="button" onclick="feedCat(${cat.id})">
                        🍖 Feed (${cat.level * 5} 🐟)
                    </button>
                </div>
            `).join('');
        }

        function getCatEmoji(type) {
            const emojis = {
                normal: '😸',
                devil: '😈',
                angel: '😇',
                fat: '🐷'
            };
            return emojis[type] || '🐱';
        }

        function collectFish() {
            state.fish += Math.floor(Math.random() * 3) + 1;
            updateFishDisplay();
            tg.HapticFeedback.impactOccurred('light');
            showTempMessage('+2 🐟 Fish collected!');
            saveGame();
        }

        function feedCat(catId) {
            const cat = state.cats.find(c => c.id === catId);
            const cost = cat.level * 5;
            
            if(state.fish >= cost) {
                state.fish -= cost;
                cat.level++;
                updateFishDisplay();
                renderCats();
                tg.HapticFeedback.notificationOccurred('success');
                showTempMessage('Cat leveled up!');
                saveGame();
            } else {
                tg.showAlert('Not enough fish!');
            }
        }

        function openShop() {
            tg.showPopup({
                title: '🐾 Cat Shop',
                message: 'What would you like to buy?',
                buttons: [
                    { id: 'devil', type: 'default', text: '😈 Devil Cat (100 🐟)' },
                    { id: 'angel', type: 'default', text: '😇 Angel Cat (100 🐟)' },
                    { type: 'cancel', text: 'Close' }
                ]
            }, (buttonId) => {
                if(buttonId === 'devil' || buttonId === 'angel') {
                    buyCat(buttonId);
                }
            });
        }

        function buyCat(type) {
            if(state.fish >= 100) {
                state.fish -= 100;
                state.cats.push({
                    id: Date.now(),
                    type: type,
                    level: 1
                });
                updateFishDisplay();
                renderCats();
                saveGame();
                tg.showAlert(`New ${type} cat added!`);
            } else {
                tg.showAlert('You need 100 🐟 to buy this cat!');
            }
        }

        function showTempMessage(text) {
            const msg = document.createElement('div');
            msg.textContent = text;
            msg.style = `
                position: fixed;
                bottom: 20px;
                left: 50%;
                transform: translateX(-50%);
                background: var(--main-color);
                color: white;
                padding: 10px 20px;
                border-radius: 20px;
                animation: fade 2s;
            `;
            document.body.appendChild(msg);
            setTimeout(() => msg.remove(), 2000);
        }

        function updateFishDisplay() {
            document.getElementById('fishCount').textContent = state.fish;
            localStorage.setItem('fish', state.fish);
        }

        function saveGame() {
            localStorage.setItem('fish', state.fish);
            localStorage.setItem('cats', JSON.stringify(state.cats));
        }

        // Start the game
        init();
    </script>
</body>
</html>