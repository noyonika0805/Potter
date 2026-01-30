<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Pottermore | Re-Imagined</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700&family=Spectral:ital,wght@0,400;0,700;1,400&display=swap');

        :root {
            --parchment: #fdf5e6;
            --ink: #2c2416;
            --gold: #c5a059;
            --gryffindor: #740001; --slytherin: #1a472a;
            --ravenclaw: #0e1a40; --hufflepuff: #ecb939;
        }

        body {
            background: #050505;
            background-image: url('https://www.transparenttextures.com/patterns/stardust.png');
            color: var(--ink);
            font-family: 'Spectral', serif;
            margin: 0;
            display: flex;
            justify-content: center;
            padding-top: 50px;
            min-height: 100vh;
        }

        #wrapper {
            width: 950px;
            background: var(--parchment);
            background-image: url('https://www.transparenttextures.com/patterns/papyrus.png');
            border: 15px solid transparent;
            border-image: url('https://www.transparenttextures.com/patterns/black-paper.png') 30 round;
            box-shadow: 0 0 100px rgba(0,0,0,0.8);
            padding: 40px;
            border-radius: 2px;
            position: relative;
        }

        header {
            text-align: center;
            border-bottom: 2px solid var(--ink);
            margin-bottom: 30px;
        }

        h1 { font-family: 'Cinzel', serif; letter-spacing: 5px; margin: 10px 0; }

        .stats-hud {
            display: flex;
            justify-content: space-between;
            background: rgba(0,0,0,0.05);
            padding: 10px 20px;
            border-radius: 5px;
            font-weight: bold;
            text-transform: uppercase;
            font-size: 14px;
        }

        #scene-viewport {
            width: 100%;
            height: 450px;
            background-color: #000;
            border: 4px solid var(--ink);
            margin: 20px 0;
            position: relative;
            overflow: hidden;
            display: flex;
            align-items: flex-end;
            justify-content: center;
            background-size: cover;
            background-position: center;
            transition: background-image 0.8s ease-in-out;
        }

        #scene-overlay {
            background: rgba(0,0,0,0.6);
            color: white;
            width: 100%;
            padding: 20px;
            text-align: center;
        }

        .action-menu {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 15px;
            margin-top: 20px;
        }

        button {
            background: var(--ink);
            color: var(--parchment);
            border: none;
            padding: 12px;
            font-family: 'Cinzel', serif;
            cursor: pointer;
            transition: 0.3s;
            border: 1px solid var(--gold);
        }

        button:hover:not(:disabled) {
            background: var(--gold);
            color: #000;
        }

        button:disabled { opacity: 0.4; cursor: not-allowed; }

        /* POTIONS MINI-GAME */
        #potions-ui {
            display: none;
            background: rgba(255,255,255,0.5);
            padding: 20px;
            border: 2px solid var(--ink);
            margin-top: 20px;
            text-align: center;
        }

        .cauldron {
            width: 80px; height: 80px;
            background: #222;
            border-radius: 50% 50% 30% 30%;
            margin: 20px auto;
            border: 5px solid #444;
            transition: 0.5s;
        }

        .inventory-shelf {
            margin-top: 30px;
            border-top: 1px solid var(--ink);
            padding-top: 10px;
            font-size: 14px;
        }

        #house-indicator {
            padding: 5px 15px;
            display: none;
            color: white;
            border-radius: 20px;
        }
    </style>
</head>
<body>

<div id="wrapper">
    <header>
        <h1>POTTERMORE</h1>
        <div id="house-indicator">HOUSE: <span id="house-name"></span></div>
    </header>

    <div class="stats-hud">
        <span>Galleons: <span id="gold">0</span></span>
        <span>Current Area: <span id="area">London</span></span>
    </div>

    <div id="scene-viewport" style="background-image: url('https://images.unsplash.com/photo-1510133769067-2788a70fa9a9?auto=format&fit=crop&q=80&w=1000');">
        <div id="scene-overlay">
            <h2 id="scene-title">Privet Drive</h2>
            <p id="scene-desc">An owl has arrived. Your journey to Hogwarts begins at Gringotts.</p>
        </div>
    </div>

    <div id="msg-box" style="text-align: center; color: #740001; font-weight: bold; margin-bottom: 10px;"></div>

    <div class="action-menu">
        <button onclick="goGringotts()" id="btn-bank">Gringotts Bank</button>
        <button onclick="goOllivanders()" id="btn-wand" disabled>Ollivanders</button>
        <button onclick="goApothecary()" id="btn-items" disabled>Apothecary</button>
        <button onclick="goSorting()" id="btn-sort" disabled>Sorting Hat</button>
        <button onclick="openPotions()" id="btn-brew" disabled>Brew Potions</button>
    </div>

    <div id="potions-ui">
        <h3>Standard Potion-Making</h3>
        <p><i>Recipe: 2x Fangs, 1x Slime, then Ignite.</i></p>
        <div id="cauldron-div" class="cauldron"></div>
        <button onclick="addIng('Fangs')">Add Snake Fangs</button>
        <button onclick="addIng('Slime')">Add Potion Base</button>
        <button onclick="heat()">Heat Fire</button>
        <button onclick="checkBrew()" style="background:green">Finish</button>
    </div>

    <div class="inventory-shelf">
        <strong>Trunk:</strong> <span id="inv-items">Empty</span>
    </div>
</div>

<script>
    let gold = 0;
    let items = [];
    let house = null;
    let cauldron = [];
    let heated = false;

    const scenes = {
        gringotts: "https://images.unsplash.com/photo-1633515327230-b5e13768d742?auto=format&fit=crop&q=80&w=1000",
        diagon: "https://images.unsplash.com/photo-1518709268805-4e9042af9f23?auto=format&fit=crop&q=80&w=1000",
        potions: "https://images.unsplash.com/photo-1581093196277-9f608fade137?auto=format&fit=crop&q=80&w=1000",
        great_hall: "https://images.unsplash.com/photo-1547756536-cbe3673ff2e0?auto=format&fit=crop&q=80&w=1000"
    };

    function update(msg) {
        document.getElementById('gold').innerText = gold;
        document.getElementById('inv-items').innerText = items.length > 0 ? items.join(", ") : "Empty";
        if(msg) document.getElementById('msg-box').innerText = msg;
        
        if(gold > 0) {
            document.getElementById('btn-wand').disabled = false;
            document.getElementById('btn-items').disabled = false;
            document.getElementById('btn-sort').disabled = false;
        }
        if(items.includes("Cauldron")) document.getElementById('btn-brew').disabled = false;
    }

    function goGringotts() {
        gold = 50;
        document.getElementById('scene-viewport').style.backgroundImage = `url(${scenes.gringotts})`;
        document.getElementById('area').innerText = "Gringotts";
        document.getElementById('scene-title').innerText = "Vault 687";
        update("The Goblins grant you 50 Galleons.");
    }

    function goOllivanders() {
        if(gold < 10) return update("Too poor!");
        gold -= 10;
        items.push("Wand");
        document.getElementById('scene-viewport').style.backgroundImage = `url(${scenes.diagon})`;
        update("The wand chooses the wizard! 10 Galleons spent.");
    }

    function goApothecary() {
        if(gold < 15) return update("Too poor!");
        gold -= 15;
        items.push("Cauldron");
        update("You bought a Pewter Cauldron. You can now brew potions.");
    }

    function goSorting() {
        const houses = ['Gryffindor', 'Slytherin', 'Ravenclaw', 'Hufflepuff'];
        house = houses[Math.floor(Math.random() * houses.length)];
        document.getElementById('scene-viewport').style.backgroundImage = `url(${scenes.great_hall})`;
        document.getElementById('house-indicator').style.display = "inline-block";
        document.getElementById('house-indicator').style.background = `var(--${house.toLowerCase()})`;
        document.getElementById('house-name').innerText = house;
        update(`Better be... ${house}!`);
    }

    function openPotions() {
        document.getElementById('potions-ui').style.display = "block";
        document.getElementById('scene-viewport').style.backgroundImage = `url(${scenes.potions})`;
    }

    function addIng(ing) {
        cauldron.push(ing);
        update(`Added ${ing}`);
    }

    function heat() {
        heated = true;
        document.getElementById('cauldron-div').style.boxShadow = "0 0 20px orange";
    }

    function checkBrew() {
        const success = cauldron.filter(x => x==='Fangs').length === 2 && 
                        cauldron.includes('Slime') && heated;
        
        if(success) {
            update("Success! Potion brewed perfectly.");
            document.getElementById('cauldron-div').style.background = "cyan";
        } else {
            update("BOOM! The cauldron melted.");
            document.getElementById('cauldron-div').style.background = "red";
        }
        cauldron = []; heated = false;
        setTimeout(() => {
            document.getElementById('cauldron-div').style.background = "#222";
            document.getElementById('cauldron-div').style.boxShadow = "none";
        }, 2000);
    }
</script>

</body>
</html>
