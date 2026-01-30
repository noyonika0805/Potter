<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Pottermore: The Original Experience</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700&family=Spectral:ital,wght@0,400;1,400&display=swap');

        body, html {
            margin: 0; padding: 0;
            height: 100%; width: 100%;
            background: #050505;
            color: #f4e4bc;
            font-family: 'Spectral', serif;
            overflow-y: hidden; /* Lock vertical scroll */
        }

        /* --- THE SCROLLING CONTAINER --- */
        #viewport {
            display: flex;
            width: 300vw; /* 3 times the screen width for scrolling */
            height: 100vh;
            overflow-x: auto;
            scroll-behavior: smooth;
        }

        /* --- PARALLAX SCENES --- */
        .scene {
            position: relative;
            min-width: 100vw;
            height: 100vh;
            background-size: cover;
            background-position: center;
            display: flex;
            flex-direction: column;
            justify-content: flex-end;
            border-right: 5px solid #2c2416;
        }

        /* Scene Images - Mocking the video's aesthetic */
        #scene-1 { background-image: linear-gradient(rgba(0,0,0,0.3), rgba(0,0,0,0.3)), url('https://images.unsplash.com/photo-1547756536-cbe3673ff2e0?q=80&w=2000'); }
        #scene-2 { background-image: linear-gradient(rgba(0,0,0,0.3), rgba(0,0,0,0.3)), url('https://images.unsplash.com/photo-1518709268805-4e9042af9f23?q=80&w=2000'); }
        #scene-3 { background-image: linear-gradient(rgba(0,0,0,0.3), rgba(0,0,0,0.3)), url('https://images.unsplash.com/photo-1510133769067-2788a70fa9a9?q=80&w=2000'); }

        .scene-text {
            background: rgba(0, 0, 0, 0.7);
            padding: 40px;
            text-align: center;
            border-top: 2px solid #c5a059;
            backdrop-filter: blur(5px);
        }

        /* --- COLLECTIBLES (The Red Glow) --- */
        .collectible {
            position: absolute;
            width: 40px; height: 40px;
            background: rgba(255, 0, 0, 0.2);
            border-radius: 50%;
            cursor: pointer;
            box-shadow: 0 0 15px rgba(255, 255, 255, 0.5);
            transition: 0.3s;
            display: flex; align-items: center; justify-content: center;
            font-size: 0; /* Hide text until found */
        }

        .collectible:hover {
            background: rgba(255, 255, 255, 0.4);
            box-shadow: 0 0 25px gold;
        }

        .collectible.found {
            background: gold !important;
            box-shadow: 0 0 30px gold;
            pointer-events: none;
        }

        /* --- TOP UI --- */
        #top-nav {
            position: fixed; top: 0; width: 100%; height: 60px;
            background: rgba(0,0,0,0.9);
            border-bottom: 1px solid #c5a059;
            display: flex; align-items: center; justify-content: space-between;
            padding: 0 30px; z-index: 1000;
            box-sizing: border-box;
        }

        .house-btn {
            font-family: 'Cinzel'; border: 1px solid gold; padding: 5px 15px;
            background: none; color: gold; cursor: pointer;
        }

    </style>
</head>
<body>

    <div id="top-nav">
        <div style="font-family: 'Cinzel'; letter-spacing: 2px;">POTTERMORE REBORN</div>
        <div id="stats">COLLECTED: <span id="found-count">0</span> / 3</div>
        <button class="house-btn" onclick="alert('Sorting Hat is currently busy...')">CHOOSE HOUSE</button>
    </div>

    <div id="viewport">
        
        <div class="scene" id="scene-3">
            <div class="collectible" style="top: 40%; left: 30%;" onclick="findItem(this, 'Hogwarts Letter')">!</div>
            <div class="scene-text">
                <h2 style="font-family: 'Cinzel';">CHAPTER 1: THE INVITATION</h2>
                <p>An owl flies over Privet Drive. Search the garden to find your letter.</p>
                <p><b>&larr; Scroll Right to Continue &rarr;</b></p>
            </div>
        </div>

        <div class="scene" id="scene-2">
            <div class="collectible" style="bottom: 30%; right: 40%;" onclick="findItem(this, '5 Galleons')">!</div>
            <div class="scene-text">
                <h2 style="font-family: 'Cinzel';">CHAPTER 2: DIAGON ALLEY</h2>
                <p>The brick wall opens. There is gold hidden near the entrance to Gringotts.</p>
            </div>
        </div>

        <div class="scene" id="scene-1">
            <div class="collectible" style="top: 50%; left: 50%;" onclick="findItem(this, 'Chocolate Frog Card')">!</div>
            <div class="scene-text">
                <h2 style="font-family: 'Cinzel';">CHAPTER 3: THE SORTING</h2>
                <p>You have arrived at Hogwarts. Look for a treat under the table.</p>
            </div>
        </div>

    </div>

    <script>
        let found = 0;

        function findItem(element, name) {
            if (!element.classList.contains('found')) {
                element.classList.add('found');
                found++;
                document.getElementById('found-count').innerText = found;
                alert("You found: " + name + "!");
                
                if (found === 3) {
                    alert("Congratulations! You've completed the Book 1 Walkthrough.");
                }
            }
        }

        // Custom horizontal scroll logic for mouse wheel
        const item = document.getElementById("viewport");
        item.addEventListener("wheel", (evt) => {
            evt.preventDefault();
            item.scrollLeft += evt.deltaY;
        });
    </script>

</body>
</html>
