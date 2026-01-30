<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Pottermore: The Journey Begins</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@500&family=Spectral:italic,wght@0,400;1,400&display=swap');

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
            overflow: hidden;
        }

        #game-world {
            position: relative;
            width: 100vw; height: 100vh;
            display: flex; justify-content: center; align-items: center;
        }

        /* --- THE LETTER --- */
        #letter-scene {
            position: absolute;
            z-index: 100;
            width: 400px;
            background: #fffef0;
            padding: 40px;
            box-shadow: 0 0 50px rgba(0,0,0,0.8);
            border: 1px solid #ddd;
            transform: rotate(-2deg);
            cursor: pointer;
            transition: 0.5s;
        }

        #letter-scene:hover { transform: rotate(0deg) scale(1.02); }

        #letter-scene.hidden { display: none; }

        .wax-seal {
            width: 60px; height: 60px;
            background: #8b0000;
            border-radius: 50%;
            margin: 20px auto;
            display: flex; align-items: center; justify-content: center;
            color: white; font-weight: bold; font-family: 'Cinzel';
        }

        /* --- THE MAP / SHOPS --- */
        #diagon-alley {
            display: none;
            width: 100%; height: 100%;
            background: url('https://images.unsplash.com/photo-1518709268805-4e9042af9f23?q=80&w=2000') center/cover;
            position: relative;
        }

        .shop-hotspot {
            position: absolute;
            background: rgba(255,255,255,0.1);
            border: 2px solid var(--gold);
            cursor: pointer;
            display: flex; align-items: center; justify-content: center;
            transition: 0.3s;
        }

        .shop-hotspot:hover { background: rgba(197, 160, 89, 0.3); }
        .shop-label { 
            color: white; font-family: 'Cinzel'; 
            text-shadow: 2px 2px 4px #000; pointer-events: none;
        }

        /* --- INTERIOR VIEW --- */
        #interior-view {
            display: none;
            position: absolute;
            width: 80%; height: 70%;
            background: var(--parchment);
            border: 10px solid var(--ink);
            box-shadow: 0 0 100px #000;
            padding: 40px;
            z-index: 50;
        }

        .item-to-find {
            display: inline-block;
            margin: 15px; padding: 10px;
            background: rgba(0,0,0,0.05);
            border: 1px dashed var(--ink);
            cursor: pointer;
        }

        .item-to-find:hover { background: var(--gold); }

        /* --- HUD --- */
        #hud {
            position: fixed; top: 20px; left: 20px;
            background: rgba(0,0,0,0.8); color: white;
            padding: 10px 20px; border-radius: 30px;
            font-family: 'Cinzel'; font-size: 14px;
        }

        .back-btn {
            position: absolute; bottom: 20px; right: 20px;
            padding: 10px 20px; background: var(--ink); color: white; border: none;
            cursor: pointer; font-family: 'Cinzel';
        }
    </style>
</head>
<body>

<div id="game-world">
    
    <div id="hud">Trunk: <span id="item-count">0</span>/3 Items Collected</div>

    <div id="letter-scene" onclick="openLetter()">
        <p>HOGWARTS SCHOOL <i>of</i> WITCHCRAFT <i>and</i> WIZARDRY</p>
        <p>Dear Wizard,</p>
        <p>We are pleased to inform you that you have been accepted at Hogwarts School of Witchcraft and Wizardry.</p>
        <p>Please find enclosed a list of all necessary books and equipment.</p>
        <div class="wax-seal">H</div>
        <p style="text-align: center; font-size: 12px;">(Click to head to Diagon Alley)</p>
    </div>

    <div id="diagon-alley">
        <div class="shop-hotspot" style="top: 30%; left: 15%; width: 200px; height: 300px;" onclick="enterShop('books')">
            <span class="shop-label">Flourish & Blotts</span>
        </div>
        <div class="shop-hotspot" style="top: 35%; right: 20%; width: 150px; height: 250px;" onclick="enterShop('wand')">
            <span class="shop-label">Ollivanders</span>
        </div>
        <div class="shop-hotspot" style="bottom: 10%; left: 45%; width: 180px; height: 200px;" onclick="enterShop('potion')">
            <span class="shop-label">Apothecary</span>
        </div>
    </div>

    <div id="interior-view">
        <h2 id="interior-title">Shop Interior</h2>
        <p id="interior-desc">Search the shelves for your items.</p>
        <div id="items-container"></div>
        <button class="back-btn" onclick="exitShop()">Return to Street</button>
    </div>

</div>

<script>
    let inventory = [];
    const totalItems = 3;

    function openLetter() {
        document.getElementById('letter-scene').classList.add('hidden');
        document.getElementById('diagon-alley').style.display = 'block';
    }

    const shopData = {
        'books': {
            title: 'Flourish & Blotts',
            desc: 'The shelves reach the ceiling. Find your spellbook!',
            items: ['Standard Book of Spells', 'Ancient Runes', 'History of Magic'],
            correct: 'Standard Book of Spells'
        },
        'wand': {
            title: 'Ollivanders',
            desc: 'Dusty boxes are everywhere. Find your wand box!',
            items: ['Broken Stick', 'Holly Wand Box', 'Old Umbrella'],
            correct: 'Holly Wand Box'
        },
        'potion': {
            title: 'Slug & Jiggers',
            desc: 'The smell is pungent. Find your Pewter Cauldron!',
            items: ['Dragon Liver', 'Pewter Cauldron', 'Glass Phials'],
            correct: 'Pewter Cauldron'
        }
    };

    function enterShop(shopKey) {
        const data = shopData[shopKey];
        const interior = document.getElementById('interior-view');
        const container = document.getElementById('items-container');
        
        document.getElementById('interior-title').innerText = data.title;
        document.getElementById('interior-desc').innerText = data.desc;
        container.innerHTML = '';

        data.items.forEach(item => {
            let div = document.createElement('div');
            div.className = 'item-to-find';
            div.innerText = item;
            div.onclick = () => collectItem(item, data.correct);
            container.appendChild(div);
        });

        interior.style.display = 'block';
    }

    function collectItem(clicked, correct) {
