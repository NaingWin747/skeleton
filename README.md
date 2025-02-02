<!DOCTYPE html>
<html>
<head>
    <title>RedDevil Airdrop 🚀</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #1a1a1a;
            color: #fff;
            padding: 20px;
        }
        .container {
            max-width: 400px;
            margin: 0 auto;
        }
        .logo {
            text-align: center;
            font-size: 2em;
            color: #ff4444;
            margin-bottom: 20px;
        }
        .card {
            background: #2a2a2a;
            padding: 20px;
            border-radius: 15px;
            margin-bottom: 20px;
        }
        button {
            background: #ff4444;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            width: 100%;
            font-size: 16px;
            cursor: pointer;
        }
        .user-info {
            margin-bottom: 15px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="logo">🔥 RedDevil Airdrop</div>
        
        <div class="card">
            <div class="user-info">
                <div>👤 User: <span id="username">Loading...</span></div>
                <div>💰 Wallet: <span id="wallet">Not connected</span></div>
            </div>

            <div id="statusMessage"></div>
            
            <button onclick="checkEligibility()" id="checkButton">
                🔍 Check Eligibility
            </button>
            
            <button onclick="claimAirdrop()" id="claimButton" style="display: none;">
                🚀 Claim 500 RDD Tokens
            </button>
        </div>
    </div>

    <script>
        // Initialize Telegram Web App
        const tg = Telegram.WebApp;
        tg.ready();
        tg.expand();

        // User data from Telegram
        const user = tg.initDataUnsafe.user;
        document.getElementById('username').textContent = user.username || `User${user.id}`;

        // Mock blockchain connection
        let walletConnected = false;
        let isEligible = false;

        function checkEligibility() {
            // Simulate eligibility check (replace with real API call)
            const mockEligibleUsers = [user.id]; // Add eligible user IDs here
            isEligible = mockEligibleUsers.includes(user.id);

            if(isEligible) {
                document.getElementById('statusMessage').innerHTML = 
                    `<div style="color: #4CAF50; margin: 10px 0;">✅ Eligible for airdrop!</div>`;
                document.getElementById('claimButton').style.display = 'block';
            } else {
                document.getElementById('statusMessage').innerHTML = 
                    `<div style="color: #ff4444; margin: 10px 0;">❌ Not eligible for airdrop</div>`;
            }
        }

        function claimAirdrop() {
            if(!walletConnected) {
                tg.showPopup({
                    title: 'Connect Wallet',
                    message: 'Please connect your wallet to claim',
                    buttons: [{ id: 'connect', type: 'default', text: 'Connect' }]
                }, (buttonId) => {
                    if(buttonId === 'connect') {
                        // Simulate wallet connection
                        walletConnected = true;
                        document.getElementById('wallet').textContent = '0x...' + user.id.toString().slice(-6);
                        claimAirdrop();
                    }
                });
                return;
            }

            // Simulate airdrop claim
            tg.MainButton.showProgress();
            setTimeout(() => {
                tg.MainButton.hideProgress();
                tg.showAlert(`🎉 500 RDD tokens claimed! Transaction hash: 0x${Math.random().toString(16).slice(2)}`);
                document.getElementById('claimButton').style.display = 'none';
            }, 2000);
        }

        // Handle closing
        tg.onEvent('viewportChanged', (e) => {
            if(e.isStateStable && tg.isExpanded) tg.close();
        });
    </script>
</body>
</html>