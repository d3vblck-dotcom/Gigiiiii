# Gigiiiii
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>DEV & KALINA // 3D UG RAPPER PENTHOUSE</title>
    <script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=VT323&family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; user-select: none; }
        body { 
            background: #010204; color: #f1f5f9; font-family: 'Plus Jakarta Sans', sans-serif; 
            display: flex; flex-direction: column; align-items: center; justify-content: center; 
            width: 100vw; height: 100vh; margin: 0; padding: 0; overflow: hidden; 
        }

        #webgl-container { position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; z-index: 1; pointer-events: none; }

        @keyframes shake {
            0% { transform: translate(1px, 1px) rotate(0deg); }
            20% { transform: translate(-6px, 2px) rotate(-2deg); }
            40% { transform: translate(-1px, -3px) rotate(1deg); }
            60% { transform: translate(5px, 1px) rotate(0deg); }
            80% { transform: translate(-2px, 3px) rotate(-1deg); }
            100% { transform: translate(0px, 0px) rotate(0deg); }
        }
        .screen-shake { animation: shake 0.4s ease-in-out; }

        #slap-cutscene {
            position: fixed; top: 0; left: 0; width: 100vw; height: 100vh;
            background: rgba(239, 68, 68, 0.85); z-index: 9999; display: none;
            flex-direction: column; align-items: center; justify-content: center;
        }

        #os-interface { 
            position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; 
            background: radial-gradient(circle at center, #130d22 0%, #030508 100%); 
            display: flex; flex-direction: column; padding: 20px; z-index: 100; justify-content: center; align-items: center; 
        }
        .os-box { 
            width: 100%; max-width: 420px; background: rgba(15, 12, 25, 0.95); 
            backdrop-filter: blur(16px); border: 1px solid rgba(255, 107, 129, 0.4); 
            border-radius: 20px; padding: 22px; display: flex; flex-direction: column; gap: 14px; 
            box-shadow: 0 0 60px rgba(255,107,129,0.2); text-align: center; 
        }
        .os-header { display: flex; justify-content: space-between; font-family: 'VT323', monospace; font-size: 15px; color: #38ef7d; border-bottom: 1px solid rgba(255,255,255,0.08); padding-bottom: 6px; }
        .os-card { background: rgba(255,255,255,0.02); border: 1px solid rgba(255, 107, 129, 0.2); padding: 12px; border-radius: 10px; text-align: left; }
        
        h1 { margin: 4px 0; font-size: 17px; font-weight: 800; color: #ff6b81; text-shadow: 0 0 20px rgba(255,107,129,0.6); }

        #simulator-frame { display: none; flex-direction: column; width: 100vw; height: 100vh; position: relative; justify-content: space-between; z-index: 10; pointer-events: none; padding: 10px; }
        
        #stats-bar { 
            display: flex; justify-content: space-between; width: 100%; max-width: 420px; margin: 0 auto;
            background: rgba(15, 23, 32, 0.85); backdrop-filter: blur(8px); padding: 8px 14px; 
            border: 1px solid rgba(56, 239, 125, 0.3); border-radius: 10px; font-family: 'VT323', monospace; font-size: 16px; align-items: center; pointer-events: auto; 
        }
        .stat-item { color: #38ef7d; font-weight: bold; text-shadow: 0 0 10px rgba(56,239,125,0.4); }

        #hud-container { display: flex; flex-direction: column; width: 100%; max-width: 420px; margin: 0 auto; gap: 8px; pointer-events: auto; }

        #wardrobe-panel { background: rgba(15, 23, 32, 0.85); backdrop-filter: blur(8px); border: 1px solid rgba(255,255,255,0.1); padding: 8px; border-radius: 12px; text-align: center; }
        .wardrobe-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 5px; margin-top: 5px; }

        #wife-controls { display: grid; grid-template-columns: repeat(2, 1fr); gap: 5px; }

        #chat-container { background: rgba(17, 27, 33, 0.9); backdrop-filter: blur(8px); border: 1px solid rgba(0,168,132,0.3); border-radius: 10px; display: flex; flex-direction: column; height: 75px; overflow: hidden; }
        #chat-messages { flex: 1; padding: 4px 8px; overflow-y: auto; display: flex; flex-direction: column; gap: 3px; font-size: 9px; }
        .chat-msg { background: rgba(32, 44, 51, 0.9); padding: 3px 6px; border-radius: 4px; max-width: 85%; word-wrap: break-word; }
        #chat-input-area { display: flex; background: rgba(32, 44, 51, 0.9); padding: 3px; border-top: 1px solid #2f3b43; }
        #chat-input { flex: 1; background: #2a3942; border: none; color: white; padding: 3px 6px; font-size: 9px; border-radius: 4px; outline: none; }

        .modal-overlay { 
            position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; 
            background: rgba(3, 5, 8, 0.96); backdrop-filter: blur(12px); display: none; 
            flex-direction: column; align-items: center; justify-content: center; z-index: 200; padding: 25px; text-align: center; 
            pointer-events: auto;
        }

        button { 
            background: linear-gradient(135deg, #ff6b81, #ff334b); border: none; color: white; padding: 8px 10px; 
            font-weight: 700; font-family: 'Plus Jakarta Sans', sans-serif; border-radius: 8px; cursor: pointer; 
            font-size: 9px; transition: all 0.2s; text-align: center; box-shadow: 0 4px 15px rgba(255,107,129,0.35); 
        }
        button:hover { filter: brightness(1.1); transform: translateY(-1px); }
        button:active { transform: scale(0.97); }
        
        .green-btn { background: linear-gradient(135deg, #38ef7d, #11998e) !important; color: #030508 !important; font-weight: 800; box-shadow: 0 4px 15px rgba(56,239,125,0.35) !important; }
        input[type="text"] { 
            background: #030508; border: 1px solid rgba(255,255,255,0.15); color: #fff; 
            padding: 8px 12px; font-size: 10px; border-radius: 6px; width: 100%; outline: none; 
        }
    </style>
</head>
<body>

    <div id="webgl-container"></div>

    <div id="slap-cutscene">
        <div style="font-size: 70px;">👋💥</div>
        <h2 style="color: white; font-size: 22px; margin-top: 10px; font-family: 'VT323'; letter-spacing: 2px;">SAVAGE SLAP APPLIED!</h2>
    </div>

    <!-- OS Setup & 4-Digit Connection Screen -->
    <div id="os-interface">
        <div class="os-box">
            <div class="os-header">
                <span>3D_PENTHOUSE_V1.0 // WEBGL</span>
                <span id="os-clock">00:00:00</span>
            </div>
            
            <div style="font-size: 32px; text-shadow: 0 0 20px #ff6b81;">🏢🔥</div>
            <h1>UG RAPPER PENTHOUSE 3D</h1>
            <p style="font-size: 10px; color: #94a3b8; margin: 0;">Immersive Real-Time WebGL Experience</p>

            <div class="os-card">
                <p style="margin: 4px 0; color: #f472b6; font-size: 11px; font-weight: 600;">Step 1: Choose Your Identity</p>
                <div style="display: flex; gap: 8px; margin-top: 8px;">
                    <button onclick="setRole('husband')" class="green-btn" style="flex:1;">Husband / Dev 🙇‍♂️</button>
                    <button onclick="setRole('wife')" style="flex:1;">Kalina (Wife) 👑</button>
                </div>
            </div>

            <div class="os-card" id="connection-box">
                <p style="margin: 4px 0; color: #38ef7d; font-size: 11px; font-weight: 600;">Step 2: Enter Room Codes to Link</p>
                <div style="display: flex; gap: 6px; margin-top: 6px;">
                    <input type="text" id="my-code-input" maxlength="4" placeholder="Your Code" style="font-family: 'VT323'; font-size: 16px; text-align: center; letter-spacing: 2px;">
                    <input type="text" id="partner-code-input" maxlength="4" placeholder="Partner Code" style="font-family: 'VT323'; font-size: 16px; text-align: center; letter-spacing: 2px;">
                </div>
                <button onclick="connectCodes()" class="green-btn" style="width: 100%; margin-top: 8px;">Connect to Partner</button>
                <p id="connection-status" style="font-size: 9px; color: #f472b6; margin-top: 6px;">Status: Unlinked</p>
            </div>

            <button id="launch-hub-btn" onclick="openPenthouse()" class="green-btn" style="width: 100%; padding: 12px; font-size: 11px; display:none; letter-spacing: 1px;">🏢 ENTER 3D PENTHOUSE</button>
        </div>
    </div>

    <!-- Main Game Frame -->
    <div id="simulator-frame">
        <div id="stats-bar">
            <span>👑 Role: <span id="current-role-display" class="stat-item">Husband</span></span>
            <span>⚡ Obedience: <span id="obedience-score" class="stat-item">100</span>%</span>
            <span>🔒 Status: <span id="cage-status" class="stat-item" style="color:#38ef7d;">Free</span></span>
        </div>

        <div id="hud-container">
            <!-- Dish Washing Pop-up -->
            <div id="dish-modal" class="modal-overlay">
                <h2 style="color: #38ef7d; font-size: 18px; font-family: 'VT323';">🧹 KALINA ASSIGNED CHORES</h2>
                <p style="font-size: 13px; color: #fff; margin: 20px 0;">"Wash dishes right now Dev!"</p>
                <div style="display: flex; gap: 10px; width: 100%; max-width: 300px;">
                    <button onclick="respondDishes(true)" class="green-btn" style="flex:1; padding: 12px;">Yes mommyy 🙇‍♂️</button>
                    <button onclick="respondDishes(false)" style="flex:1; padding: 12px;">No 💀</button>
                </div>
            </div>

            <!-- Cage Release Pop-up -->
            <div id="cage-modal" class="modal-overlay">
                <h2 style="color: #ef4444; font-size: 22px; font-family: 'VT323';">🔒 YOU ARE LOCKED IN THE 3D CAGE!</h2>
                <p style="font-size: 13px; color: #fff; margin: 20px 0;">Your obedience fell to 10% or lower!</p>
                <button onclick="begForRelease()" class="green-btn" style="width: 250px; padding: 12px;">Beg For Mercy 🥺</button>
            </div>

            <!-- "Doing It" Notification for Kalina -->
            <div id="doing-it-modal" class="modal-overlay">
                <h2 style="color: #f472b6; font-size: 20px; font-family: 'VT323';">👀 HUSBAND REQUEST</h2>
                <p style="font-size: 14px; color: #fff; margin: 20px 0; font-weight: 700;">"Mommyy can we please do it i miss ur body"</p>
                <button onclick="closeDoingItModal()" class="green-btn" style="width: 200px; padding: 12px;">Acknowledge 💖</button>
            </div>

            <!-- 6 Underground Rapper Outfits -->
            <div id="wardrobe-panel">
                <span style="font-size: 10px; color: #f472b6; font-weight: 800;">🧥 3D RAPPER OUTFITS & DRIP</span>
                <div class="wardrobe-grid">
                    <button onclick="selectOutfit('Oversized Hoodie', 0xff2e63)">1. Hoodie</button>
                    <button onclick="selectOutfit('Chrome Chains', 0xc0c0c0)">2. Chains</button>
                    <button onclick="selectOutfit('Tactical Vest', 0x333333)">3. Tactical</button>
                    <button onclick="selectOutfit('Techwear Mask', 0x0f0f0f)">4. Techwear</button>
                    <button onclick="selectOutfit('Diamond Puffer', 0x38ef7d)">5. Puffer</button>
                    <button onclick="selectOutfit('Spike Leather', 0xff00ff)">6. Spike</button>
                </div>
            </div>

            <!-- Wife Action Deck -->
            <div id="wife-controls">
                <button onclick="sendAction('bark')" style="background: linear-gradient(135deg, #ef4444, #dc2626); grid-column: span 2; padding: 8px;">🐶 COMMAND: BARK</button>
                <button onclick="sendAction('chores')">🧹 Assign Dishes</button>
                <button onclick="sendAction('slap')" style="background: #dc2626;">👋 Slap Husband</button>
                <button onclick="triggerDoingIt()" class="green-btn" id="doing-it-btn">🔥 Doing it 👀👀</button>
            </div>

            <!-- Integrated Live Chat Room -->
            <div id="chat-container">
                <div id="chat-messages">
                    <div class="chat-msg">💬 3D Penthouse secure channel active...</div>
                </div>
                <div id="chat-input-area">
                    <input type="text" id="chat-input" placeholder="Type message..." autocomplete="off">
                    <button onclick="sendChatMessage()" class="green-btn" style="padding: 3px 8px;">Send</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // --- THREE.JS 3D PENTHOUSE ENGINE ---
        let scene, camera, renderer, avatarMesh, cageMesh;
        let targetColor = 0xff2e63;

        function init3D() {
            const container = document.getElementById('webgl-container');
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x020408);
            scene.fog = new THREE.FogExp2(0x020408, 0.035);

            camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.set(0, 4, 10);
            camera.lookAt(0, 1.5, 0);

            renderer = new THREE.WebGLRenderer({ antialias: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            renderer.shadowMap.enabled = true;
            container.appendChild(renderer.domElement);

            // Lights
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.4);
            scene.add(ambientLight);

            const neonLight1 = new THREE.PointLight(0xff2e63, 3, 20);
            neonLight1.position.set(-5, 5, -2);
            scene.add(neonLight1);

            const neonLight2 = new THREE.PointLight(0x38ef7d, 3, 20);
            neonLight2.position.set(5, 5, -2);
            scene.add(neonLight2);

            // Penthouse Floor (Reflective Dark Grid)
            const floorGeo = new THREE.PlaneGeometry(30, 30);
            const floorMat = new THREE.MeshStandardMaterial({ color: 0x0a0f18, roughness: 0.2, metalness: 0.8 });
            const floor = new THREE.Mesh(floorGeo, floorMat);
            floor.rotation.x = -Math.PI / 2;
            scene.add(floor);

            const grid = new THREE.GridHelper(30, 30, 0x38ef7d, 0x112233);
            grid.position.y = 0.01;
            scene.add(grid);

            // 3D Avatar (Stylized Cyber Figure representing Dev)
            const avatarGroup = new THREE.Group();
            
            const bodyMat = new THREE.MeshStandardMaterial({ color: targetColor, roughness: 0.3, metalness: 0.5 });
            const bodyGeo = new THREE.CylinderGeometry(0.6, 0.4, 2, 16);
            const body = new THREE.Mesh(bodyGeo, bodyMat);
            body.position.y = 1;
            body.castShadow = true;
            avatarGroup.add(body);

            const headMat = new THREE.MeshStandardMaterial({ color: 0xffdbac, roughness: 0.5 });
            const headGeo = new THREE.SphereGeometry(0.4, 16, 16);
            const head = new THREE.Mesh(headGeo, headMat);
            head.position.y = 2.3;
            avatarGroup.add(head);

            avatarMesh = avatarGroup;
            scene.add(avatarMesh);

            // 3D Cage Structure (Hidden by default)
            const cageGeo = new THREE.BoxGeometry(3, 4, 3);
            const cageMat = new THREE.MeshBasicMaterial({ color: 0xef4444, wireframe: true });
            cageMesh = new THREE.Mesh(cageGeo, cageMat);
            cageMesh.position.set(0, 2, 0);
            cageMesh.visible = false;
            scene.add(cageMesh);

            window.addEventListener('resize', onWindowResize);
            animate3D();
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        let clock = new THREE.Clock();
        function animate3D() {
            requestAnimationFrame(animate3D);
            const elapsedTime = clock.getElapsedTime();

            // Gentle floating/breathing animation for avatar
            if(avatarMesh) {
                avatarMesh.position.y = Math.sin(elapsedTime * 2) * 0.05;
                avatarMesh.rotation.y = Math.sin(elapsedTime * 0.5) * 0.2;
                
                // Smooth color transition for outfit changes
                avatarMesh.children[0].material.color.lerp(new THREE.Color(targetColor), 0.1);
            }

            renderer.render(scene, camera);
        }

        // --- APPLICATION LOGIC & P2P SYNC ---
        setInterval(() => {
            document.getElementById('os-clock').innerText = new Date().toLocaleTimeString();
        }, 1000);

        let peer = null;
        let conn = null;
        let myRole = '';
        let obedience = 100;
        let isCaged = false;

        function setRole(role) {
            myRole = role;
            document.getElementById('current-role-display').innerText = role === 'wife' ? 'Kalina (Wife 👑)' : 'Husband 🙇‍♂️';
            document.getElementById('launch-hub-btn').style.display = 'block';
            
            if(role === 'husband') {
                document.getElementById('doing-it-btn').style.display = 'block';
                document.getElementById('wife-controls').style.opacity = '0.35';
                document.getElementById('wife-controls').style.pointerEvents = 'none';
                document.getElementById('wardrobe-panel').style.opacity = '0.35';
                document.getElementById('wardrobe-panel').style.pointerEvents = 'none';
            } else {
                document.getElementById('doing-it-btn').style.display = 'none';
                document.getElementById('wife-controls').style.opacity = '1';
                document.getElementById('wife-controls').style.pointerEvents = 'auto';
                document.getElementById('wardrobe-panel').style.opacity = '1';
                document.getElementById('wardrobe-panel').style.pointerEvents = 'auto';
            }
        }

        function connectCodes() {
            const myCode = document.getElementById('my-code-input').value.trim();
            const partnerCode = document.getElementById('partner-code-input').value.trim();
            if(myCode.length !== 4 || partnerCode.length !== 4) { alert("Please enter both 4-digit codes!"); return; }
            
            peer = new Peer("l1na-3d-" + myCode);
            
            peer.on('open', (id) => {
                conn = peer.connect("l1na-3d-" + partnerCode);
                setupConnection();
                document.getElementById('connection-status').innerText = "Status: Connected to " + partnerCode + " 🔗";
            });

            peer.on('connection', (connection) => {
                conn = connection;
                setupConnection();
                document.getElementById('connection-status').innerText = "Status: Partner Connected Live! 🔗";
            });
        }

        function setupConnection() {
            conn.on('data', (data) => { handleIncomingData(data); });
        }

        function openPenthouse() {
            document.getElementById('os-interface').style.display = 'none';
            document.getElementById('simulator-frame').style.display = 'flex';
            init3D();
        }

        function sendAction(actionType) {
            if(conn && conn.open) conn.send({ type: 'action', action: actionType });
            processActionLocally(actionType);
        }

        function selectOutfit(outfitName, hexColor) {
            targetColor = hexColor;
            if(conn && conn.open) conn.send({ type: 'outfit', name: outfitName, color: hexColor });
        }

        function sendChatMessage() {
            const input = document.getElementById('chat-input');
            const text = input.value.trim();
            if(!text) return;
            appendChatMsg((myRole === 'wife' ? 'Kalina: ' : 'Dev: ') + text);
            if(conn && conn.open) conn.send({ type: 'chat', text: text, sender: myRole });
            input.value = '';
        }

        function appendChatMsg(msg) {
            const box = document.getElementById('chat-messages');
            const div = document.createElement('div');
            div.className = 'chat-msg';
            div.innerText = msg;
            box.appendChild(div);
            box.scrollTop = box.scrollHeight;
        }

        function triggerDoingIt() {
            if(conn && conn.open) conn.send({ type: 'doing_it' });
            alert("❤️ Request sent to Kalina!");
        }

        function closeDoingItModal() {
            document.getElementById('doing-it-modal').style.display = 'none';
        }

        function respondDishes(agreed) {
            document.getElementById('dish-modal').style.display = 'none';
            const msg = agreed ? "Dev said yes mommyy 🧹" : "Dev said no 💀";
            if(conn && conn.open) conn.send({ type: 'dish_response', text: msg, agreed: agreed });
            if(agreed) updateObedience(10); else updateObedience(-15);
        }

        function updateObedience(amount) {
            obedience = Math.max(0, Math.min(100, obedience + amount));
            document.getElementById('obedience-score').innerText = obedience;
            if(obedience <= 10 && !isCaged) triggerCageMode();
            if(conn && conn.open) conn.send({ type: 'sync_obedience', score: obedience, caged: isCaged });
        }

        function triggerCageMode() {
            isCaged = true;
            if(cageMesh) cageMesh.visible = true;
            document.getElementById('cage-status').innerText = "LOCKED 🔒";
            document.getElementById('cage-status').style.color = "#ef4444";
            if(myRole === 'husband') document.getElementById('cage-modal').style.display = 'flex';
        }

        function begForRelease() {
            if(conn && conn.open) conn.send({ type: 'beg_release' });
            alert("🥺 Plea sent to Kalina.");
        }

        function releaseFromCage() {
            isCaged = false;
            if(cageMesh) cageMesh.visible = false;
            obedience = 50;
            document.getElementById('obedience-score').innerText = obedience;
            document.getElementById('cage-status').innerText = "Free";
            document.getElementById('cage-status').style.color = "#38ef7d";
            document.getElementById('cage-modal').style.display = 'none';
            alert("🔓 Kalina released you from the 3D cage!");
        }

        function triggerSlapFX() {
            const cutscene = document.getElementById('slap-cutscene');
            document.body.classList.add('screen-shake');
            cutscene.style.display = 'flex';
            setTimeout(() => { cutscene.style.display = 'none'; document.body.classList.remove('screen-shake'); }, 500);
        }

        function handleIncomingData(data) {
            if(data.type === 'action') processActionLocally(data.action);
            if(data.type === 'outfit') targetColor = data.color;
            if(data.type === 'doing_it') document.getElementById('doing-it-modal').style.display = 'flex';
            if(data.type === 'chat') appendChatMsg((data.sender === 'wife' ? 'Kalina: ' : 'Dev: ') + data.text);
            if(data.type === 'dish_response') {
                alert("📢 " + data.text);
                if(data.agreed) updateObedience(10); else updateObedience(-15);
            }
            if(data.type === 'sync_obedience') {
                obedience = data.score;
                document.getElementById('obedience-score').innerText = obedience;
                if(data.caged && !isCaged) triggerCageMode();
            }
            if(data.type === 'beg_release') {
                if(myRole === 'wife' && confirm("Husband is begging to be released from the 3D cage. Grant mercy?")) {
                    if(conn && conn.open) conn.send({ type: 'grant_freedom' });
                    releaseFromCage();
                }
            }
            if(data.type === 'grant_freedom') releaseFromCage();
        }

        function processActionLocally(action) {
            if(action === 'bark') {
                updateObedience(-10);
                alert("🐶 Dev barked on the 3D stage!");
            } else if(action === 'chores') {
                if(myRole === 'husband') document.getElementById('dish-modal').style.display = 'flex';
                else alert("🧹 Dish washing assigned to Dev!");
            } else if(action === 'slap') {
                triggerSlapFX();
                updateObedience(-5);
            }
        }
    </script>
</body>
</html>
