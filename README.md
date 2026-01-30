<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Pottermore | Interactive Experience</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700&family=Spectral:ital,wght@0,400;1,400&display=swap');

        :root {
            --parchment: #f4e4bc;
            --ink: #2c2416;
            --gold: #c5a059;
        }

        body, html {
            margin: 0; padding: 0;
            background: #050505;
            font-family: 'Spectral', serif;
            color: var(--ink);
            height: 100vh; overflow: hidden;
        }

        /* --- STAGE SYSTEM --- */
        .stage {
            display: none;
            width: 100vw; height: 100vh;
            position: relative;
            animation: fadeIn 1s ease-in;
        }

        #stage-letter { display: flex; justify-content: center; align-items: center; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* --- THE LETTER --- */
        #letter {
            width: 450px;
            background: #fffef0;
            padding: 50px;
            box-shadow: 0 0 50px rgba(0,0,0,0.8);
            border: 1px solid #ddd;
            transform: rotate(-1deg);
            cursor: pointer;
            text-align: center;
        }

        .seal {
            width: 60px; height: 60px; background: #8b0000;
            border-radius: 50%; margin: 20px auto;
            color: white; line-height: 60px; font-family: 'Cinzel';
        }

        /* --- DIAGON ALLEY --- */
        #diagon-alley {
            background: url('https://images.unsplash.com/photo-1518709268805-4e9042af9f23?q=80&w=2000') center/cover;
        }

        .shop-trigger {
            position: absolute;
            padding: 20px;
            background: rgba(0,0,0,0.6);
            border: 2px solid var(--gold);
            color: white;
            font-family: 'Cinzel';
            cursor: pointer;
            transition: 0.3s;
            text-align: center;
        }

        .shop-trigger:hover { background: var(--gold); color: black; transform: scale(1.1); }

        /* --- SHOP INTERIORS --- */
        #interior-view {
            background-color: var(--parchment);
            background-image: url('https://www.transparenttextures.com/patterns/pinstriped-suit.png');
            flex-direction: column; align-items: center; justify-content: center;
        }

        .item-box {
            display: inline-block;
            margin: 20px; padding: 20px;
            border: 2px solid var(--ink);
            cursor: pointer;
            transition: 0.2s;
        }

        .item-box:hover { background: var(--gold); }

        /* --- HUD --- */
        #hud {
            position: fixed; top: 0; width: 100%;
            background: rgba(0,0,0,0.8); color: white;
            padding: 15px; display: flex; justify-content: space-around;
            font-family: 'Cinzel'; z-index: 1000;
        }

        .back-btn {
            margin-top: 30px; padding: 10px 30px;
            background: var(--ink); color: white; border: none; cursor: pointer;
        }
    </style>
</head>
<body>

    <div id="hud" style="display:none;">
        <span>Trunk: <span id="trunk-count">0</span> / 3 Items Found</span>
        <span id="location-name">Diagon Alley</span>
    </div>

    <div id="stage-letter" class="stage" style="display:flex;">
        <div id="letter" onclick="startJourney()">
            <h2 style="font-family: 'Cinzel'">HOGWARTS</h2>
            <p><i>Dear Wizard,</i></p>
            <p>You have been accepted to Hogwarts School of Witchcraft and Wizardry.</p>
            <p>Click this letter to collect your supplies in Diagon Alley.</p>
            <div class="seal">H</div>
        </div>
    </div>

    <div id="stage-diagon" class="stage">
        <div class="shop-trigger" style="top: 20%; left: 20%;" onclick="openShop('books')">
            Flourish & Blotts<br><small>(Books)</small>
        </div>
        <div class="shop-trigger" style="top: 30%; right: 25%;" onclick="openShop('wand')">
            Ollivanders<br><small>(Wands)</small>
        </div>
        <div class="shop-trigger" style="bottom: 20%; left: 45%;" onclick="openShop('potions')">
            Apothecary<br><small>(Cauldrons)</small>
        </div>
    </div>

    <div id="stage-interior" class="stage" style="display:none; flex-direction: column; align-items: center; justify-content: center;">
        <h1 id="shop-title" style="font-family: 'Cinzel';">Shop Name</h1>
        <p id="shop-desc">Find your items on the shelves.</p>
        <div id="item-grid"></div>
        <button class="back-btn" onclick="showDiagon()">Back to the Street</button>
    </div>

    <script>
        let inventory = [];

        function startJourney() {
            document.getElementById('stage-letter').style.display = 'none';
            document.getElementById('stage-diagon').style.display = 'block';
            document.getElementById('hud').style.display = 'flex';
        }

        const shopData = {
            'books': {
                name: 'Flourish & Blotts',
                desc: 'Find the Standard Book of Spells!',
                items: ['History of Magic', 'Standard Book of Spells', 'Ancient Runes'],
                target: 'Standard Book of Spells'
            },
            'wand': {
                name: 'Ollivanders',
                desc: 'The wand chooses the wizard. Pick a box.',
                items: ['Old Box', 'Holly Wand', 'Bent Stick'],
                target: 'Holly Wand'
            },
            'potions': {
                name: 'Slug & Jiggers',
                desc: 'Search for a Pewter Cauldron.',
                items: ['Glass Phials', 'Bat Spleens', 'Pewter Cauldron'],
                target: 'Pewter Cauldron'
            }
        };

        function openShop(id) {
            const data = shopData[id];
            document.getElementById('stage-diagon').style.display = 'none';
            document.getElementById('stage-interior').style.display = 'flex';
            
            document.getElementById('shop-title').innerText = data.name;
            document.getElementById('shop-desc').innerText = data.desc;
            document.getElementById('location-name').innerText = data.name;

            const grid = document.getElementById('item-grid');
            grid.innerHTML = '';
            
            data.items.forEach(item => {
                const div = document.createElement('div');
                div.className = 'item-box';
                div.innerText = item;
                div.onclick = () => {
                    if(item === data.target) {
                        if(!inventory.includes(item)) {
                            inventory.push(item);
                            document.getElementById('trunk-count').innerText = inventory.length;
                            alert("Found it! Added to trunk.");
                            if(inventory.length === 3) alert("You have everything! Ready for the Hogwarts Express?");
                        } else {
                            alert("You already have this!");
                        }
                    } else {
                        alert("Not what you're looking for...");
                    }
                };
                grid.appendChild(div);
            });
        }

        function showDiagon() {
            document.getElementById('stage-interior').style.display = 'none';
            document.getElementById('stage-diagon').style.display = 'block';
            document.getElementById('location-name').innerText = "Diagon Alley";
        }
    </script>
</body>
</html>
