<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Office Quest</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
    
    <style>
        body {
            font-family: 'Press Start 2P', cursive;
            background-color: #121212; 
            color: #E0E0E0; 
            display: flex;
            flex-direction: column;
            align-items: center;
            padding-top: 10px; 
            min-height: 100vh;
            margin: 0;
            overflow-x: hidden; 
        }
        #main-controls { margin-bottom:10px; display: flex; justify-content: center; width: 100%;} 
        #main-layout {
            display: flex;
            flex-direction: row;
            align-items: flex-start; 
            gap: 15px; 
            width: 100%;
            max-width: 1100px; 
            justify-content: center;
        }
        #game-and-messages { display: flex; flex-direction: column; align-items: center; }
        #game-container {
            border: 4px solid #4A4A4A; 
            background-color: #000000; 
            box-shadow: 0 0 20px #FFEB3B; 
            position: relative; 
        }
        canvas { display: block; image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; background-color: #000000; cursor: default; }
        .ui-element { 
            background-color: #333333; 
            padding: 8px 12px; 
            border-radius: 8px; 
            border: 2px solid #555555; 
            min-width: auto; 
            text-align: center; 
            font-size: 0.75em; 
            color: #FFEB3B; 
            display: flex; 
            align-items: center; 
            justify-content: center; 
            cursor: default; 
        }
        #player-position-display { color: #80DEEA; cursor: pointer; text-decoration: underline;}
        #player-position-display:hover { color: #A7FFEB; }
        
        #shop-hud-button {
            background-color: #FFD700; 
            color: #1C1C1C; 
            cursor: pointer;
        }
        #shop-hud-button:hover {
            background-color: #FFEC8B; 
        }
        .money-sign {
            color: #4CAF50; 
            font-weight: bold;
            margin-left: 4px;
        }
        #resetGameButton { 
            background-color: #D32F2F; 
            color: white !important; 
            border-color: #B71C1C !important; 
            cursor: pointer; 
        }
        #resetGameButton:hover { background-color: #E57373 !important; }


        #hud { display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 10px; justify-content: center; max-width: 100%; align-items: center;}
        #message-log { width: 100%; max-width: 640px; min-height: 30px; background-color: rgba(20,20,20,0.8); border: 2px solid #444444; border-radius: 8px; padding: 8px; text-align: center; font-size: 0.75em; box-sizing: border-box; margin-top: 10px; color: #FFF9C4; }
        .modal { display: none; position: fixed; z-index: 100; left: 0; top: 0; width: 100%; height: 100%; overflow: auto; background-color: rgba(0,0,0,0.9); align-items: center; justify-content: center; padding: 10px; box-sizing: border-box; }
        .modal-content { background-color: #2C2C2C; margin: auto; padding: 15px; border: 3px solid #777777; border-radius: 10px; width: 90%; max-width: 600px; text-align: center; box-shadow: 0 5px 15px rgba(0,0,0,0.5); font-size: 0.9em; color: #D7CCC8; }
        .modal-content h2 { margin-top: 0; margin-bottom: 15px; color: #FFC107; font-size: 1.2em; }
        .modal-content label { display: block; margin-top: 10px; margin-bottom: 3px; text-align: left; color: #B0BEC5; }
        .modal-content input[type="text"], .modal-content input[type="date"], .modal-content select, .modal-content textarea { font-family: 'Press Start 2P', cursive; width: calc(100% - 20px); padding: 8px; margin-bottom: 10px; border: 1px solid #555555; background-color: #1E1E1E; color: #ECEFF1; border-radius: 5px; box-sizing: border-box; font-size: 0.9em; }
        .modal-content textarea { min-height: 60px; resize: vertical; } 
        .modal-content button { font-family: 'Press Start 2P', cursive; background-color: #555555; color: #FFEB3B; padding: 10px 15px; margin: 10px 5px 0; border: 2px solid #777777; border-radius: 5px; cursor: pointer; transition: background-color 0.3s; }
        .modal-content button:hover { background-color: #6A6A6A; }
        .shop-item button:disabled, .task-item button:disabled, .inventory-item button:disabled { background-color: #404040 !important; color: #888888 !important; cursor: not-allowed !important; }
        #interaction-prompt, #castle-tooltip { 
            position: absolute; 
            background-color: rgba(10,10,10, 0.85); 
            color: #FFEB3B; 
            padding: 6px 10px; 
            border-radius: 5px; 
            font-size: 0.8em; 
            display: none; 
            text-align: center; 
            border: 1px solid #FFC107; 
            z-index: 20; 
            pointer-events: none; 
        }
        #interaction-prompt { bottom: 5px; left: 50%; transform: translateX(-50%);}
        #castle-tooltip {} 


        #mobile-controls { display: none; margin-top: 10px; text-align: center; }
        #mobile-controls button { font-family: 'Press Start 2P', cursive; background-color: #4A4A4A; color: white; padding: 10px; margin: 3px; border: 2px solid #6A6A6A; border-radius: 50%; width: 50px; height: 50px; font-size: 1em; cursor: pointer; }
        #mobile-controls .action-button { padding: 8px 12px; width: auto; height: auto; border-radius: 8px; font-size: 0.9em; background-color: #03A9F4; }
        #task-list { max-height: 220px; overflow-y: auto; border: 1px solid #444444; padding: 10px; margin-top: 5px; border-radius: 5px; background-color: rgba(10,10,10,0.5); }
        .task-item { background-color: #444444; padding: 8px; margin-bottom: 8px; border-radius: 5px; display: flex; justify-content: space-between; align-items: center; }
        .task-item p { margin: 0; flex-grow: 1; font-size: 0.85em; color: #FFFDE7; }
        .task-item .task-info { display: flex; flex-direction: column; align-items: flex-start; }
        .task-item .task-info .due-date-text { font-size: 0.7em; margin-top: 2px; }
        .task-item .task-details { font-size: 0.7em; color: #CFD8DC; }
        .task-item button { padding: 6px 10px; font-size: 0.8em; margin-left: 10px; flex-shrink: 0; }
        .task-completed { text-decoration: line-through; color: #9E9E9E !important; }
        .task-completed p { color: #9E9E9E !important; }
        .task-completed button { background-color: #4CAF50 !important; color: white !important; }
        .due-today { color: #FF5252 !important; } .due-tomorrow { color: #FFAB40 !important; } .due-soon { color: #FFFF00 !important; } .overdue { color: #9E9E9E !important; text-decoration: line-through; }
        
        .sidebar { 
            width: 220px; 
            min-height: 480px; 
            background-color: #2C2C2C; 
            border: 3px solid #777777; 
            border-radius: 10px; 
            padding: 15px; 
            box-sizing: border-box; 
            color: #D7CCC8; 
            display: flex; 
            flex-direction: column;
        }
        .sidebar h3 { color: #FFC107; font-size: 1.1em; margin-top: 0; margin-bottom: 10px; text-align: center; }
        .sidebar-item-list { flex-grow: 1; max-height: calc(480px - 80px); overflow-y: auto; margin-bottom: 10px;} 
        
        #inventory-sidebar {} 
        #inventory-items-display {} 
        .inventory-item { background-color: #444444; padding: 8px; margin-bottom: 8px; border-radius: 5px; display: flex; justify-content: space-between; align-items: center; font-size: 0.85em; }
        .inventory-item span { flex-grow: 1; }
        .inventory-item button { font-family: 'Press Start 2P', cursive; background-color: #0288D1; color: white; padding: 5px 8px; font-size: 0.75em; border: 1px solid #01579B; border-radius: 4px; cursor: pointer; margin-left: 8px; }
        .inventory-item button:hover { background-color: #03A9F4; }

        #overdue-tasks-sidebar {} 
        #overdue-tasks-list {} 
        .overdue-task-item {
            background-color: #5d3a3a; 
            padding: 8px;
            margin-bottom: 8px;
            border-radius: 5px;
            font-size: 0.8em;
            border: 1px solid #865353;
        }
        .overdue-task-item .description { color: #FFCDD2; }
        .overdue-task-item .days-overdue { color: #EF9A9A; font-size: 0.7em; margin-top: 3px; }


        .hidden-fields { display: none; } 
        .extend-due-date-item { padding: 5px; margin: 5px 0; border: 1px solid #666; border-radius: 4px; cursor: pointer; }
        .extend-due-date-item:hover { background-color: #555; }

        @media (max-width: 1024px) { 
            #main-layout {
                flex-direction: column;
                align-items: center;
            }
            .sidebar { 
                width: 100%;
                max-width: 640px; 
                min-height: auto; 
                margin-top: 20px;
            }
            .sidebar-item-list {
                max-height: 200px; 
            }
        }
        @media (max-width: 768px) { body { overflow-y: auto; } .ui-element { font-size: 0.7em; padding: 6px 8px; min-width: 90px;} #hud { gap: 5px; } .modal-content { width: 95%; padding: 10px; font-size: 0.8em; } .modal-content h2 { font-size: 1em; margin-bottom: 10px;} .modal-content input, .modal-content select, .modal-content button, .modal-content textarea { font-size: 0.85em; padding: 8px;} #mobile-controls { display: block; } #task-list { max-height: 140px; } }
    </style>
</head>
<body>
    <h1 class="text-2xl sm:text-3xl mb-2 text-yellow-400">Office Quest</h1>
    <div id="hud">
        <div id="player-name-display" class="ui-element">Nombre: -</div>
        <div id="player-position-display" class="ui-element">Puesto: -</div>
        <div id="xp-display" class="ui-element">XP: 0</div>
        <div id="gold-display" class="ui-element">Oro: 0</div>
        <div id="shop-hud-button" class="ui-element" title="Abrir Emporio del Aventurero">
            Tienda <span class="money-sign">$</span>
        </div>
        <div id="resetGameButton" class="ui-element">Reiniciar</div> 
    </div>

    <div id="main-layout">
        <div id="game-and-messages">
            <div id="game-container">
                <canvas id="gameCanvas"></canvas>
                <div id="interaction-prompt">Presiona [E] para interactuar</div>
                <div id="castle-tooltip">Publica tus edictos aquí</div>
            </div>
            <div id="message-log">Cargando Office Quest...</div>
        </div>
        <div id="inventory-sidebar" class="sidebar">
            <h3>Inventario</h3>
            <div id="inventory-items-display" class="sidebar-item-list">
            </div>
        </div>
        <div id="overdue-tasks-sidebar" class="sidebar">
            <h3>Edictos Urgentes</h3>
            <div id="overdue-tasks-list" class="sidebar-item-list">
            </div>
        </div>
    </div>
    
    <div id="mobile-controls">
        <div><button id="btn-up">↑</button></div>
        <div><button id="btn-left">←</button><button id="btn-action">E</button><button id="btn-right">→</button></div>
        <div><button id="btn-down">↓</button></div>
    </div>

    <div id="userSetupModal" class="modal"> 
        <div class="modal-content">
            <h2>Registro de Héroe</h2>
            <label for="playerName">Nombre de Héroe:</label>
            <input type="text" id="playerName" placeholder="Ej: Sir Reginald">
            <label for="playerPosition">Título Nobiliario/Rol:</label>
            <input type="text" id="playerPosition" placeholder="Ej: Cazador de Dragones">
            <button id="completeSetupButton">Comenzar Gesta</button>
        </div>
    </div>

    <div id="welcomeModal" class="modal">
        <div class="modal-content">
            <h2>¡Bienvenido, Noble Héroe!</h2>
            <p>Tu leyenda comienza ahora. El reino confía en tu valor y pericia.</p>
            <p class="mt-2">Como muestra de gratitud por unirte a esta causa, el Rey te otorga una <strong class="text-yellow-400">Poción de Vida</strong>. ¡Úsala sabiamente para ganar más tiempo en tus edictos!</p>
            <button id="startAdventureButton">¡Comenzar Aventura!</button>
        </div>
    </div>
    
    <div id="extendDueDateModal" class="modal">
        <div class="modal-content">
            <h2>Extender Plazo de Edicto</h2>
            <p>Selecciona un edicto de "Trabajo del Reino" para extender su plazo por 3 días:</p>
            <div id="extendable-tasks-list" class="my-3 max-h-48 overflow-y-auto">
                </div>
            <button id="closeExtendDueDateModalButton">Cancelar</button>
        </div>
    </div>


    <div id="personalDetailsModal" class="modal">
        <div class="modal-content">
            <h2>Pergamino del Héroe</h2>
            <label for="playerLikes">Gustos del Héroe:</label>
            <textarea id="playerLikes" placeholder="Justas, hidromiel, explorar..."></textarea>
            <label for="playerAspirations">Anhelos del Héroe:</label>
            <textarea id="playerAspirations" placeholder="Encontrar un artefacto, unificar el reino..."></textarea>
            <label for="playerPersonalMission">Misión Personal:</label>
            <textarea id="playerPersonalMission" placeholder="Proteger a los débiles, buscar sabiduría..."></textarea>
            <label for="playerStory">Un Poco de Historia:</label>
            <textarea id="playerStory" placeholder="Nacido en tierras lejanas, en busca de..."></textarea>
            <button id="savePersonalDetailsButton">Guardar Crónica</button>
            <button id="closePersonalDetailsButton">Cerrar Pergamino</button>
        </div>
    </div>

    <div id="taskModal" class="modal">
        <div class="modal-content">
            <h2>Castillo de Edictos</h2>
            <div>
                <label for="missionType">Tipo de Edicto:</label>
                <select id="missionType">
                    <option value="Trabajo">Trabajo del Reino</option>
                    <option value="Personal">Cruzada Personal</option>
                </select>

                <div id="trabajoFields">
                    <label for="taskDescription">Descripción del Edicto:</label>
                    <input type="text" id="taskDescription" placeholder="Descripción del edicto">
                    
                    <label for="requiredClass">Clase de Habilidad Requerida:</label>
                    <select id="requiredClass">
                        <option value="Guerrero">Guerrero (Intensidad y Acción)</option>
                        <option value="Mago">Mago (Inteligencia y Análisis)</option>
                        <option value="Paladin">Paladín (Entrega y Sacrificio)</option>
                        <option value="Bardo">Bardo (Creatividad y Carisma)</option>
                    </select>
                    
                    <label for="taskDifficulty">Dificultad del Edicto:</label> 
                    <select id="taskDifficulty">
                        <option value="Simple">Simple</option>
                        <option value="Complicado">Complicado</option>
                        <option value="Complejo">Complejo</option>
                        <option value="Caotico">Caótico</option>
                    </select>

                    <label for="taskDueDate">Plazo del Edicto (Opcional):</label>
                    <input type="date" id="taskDueDate">
                </div>

                <div id="personalFields" class="hidden-fields">
                    <label for="personalTaskTitle">Título de la Cruzada:</label>
                    <input type="text" id="personalTaskTitle" placeholder="Ej: Aprender Alquimia">
                    <label for="currentState">Estado Actual:</label>
                    <textarea id="currentState" placeholder="Describa su situación inicial..."></textarea>
                    <label for="idealState">Estado Ideal:</label>
                    <textarea id="idealState" placeholder="Describa su meta final..."></textarea>
                    <label for="stepsToAchieve">Pasos a Seguir:</label>
                    <textarea id="stepsToAchieve" placeholder="1. Conseguir ingredientes...\n2. Estudiar el grimorio..."></textarea>
                </div>
                <button id="addTaskButton">Publicar Edicto en el Mapa</button>
            </div>
            <h3 class="text-yellow-400 mt-4">Mis Edictos Activos:</h3>
            <div id="task-list"></div>
            <button id="closeTaskModalButton">Salir del Castillo</button>
        </div>
    </div>

    <div id="shopModal" class="modal">
        <div class="modal-content">
            <h2 id="shopTitleElement">Emporio del Aventurero</h2>
            <div id="shop-items"></div>
            <button id="closeShopButton">Salir del Emporio</button>
        </div>
    </div>

    <script> 
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const TILE_SIZE = 32;
        const ROWS = 15; 
        const COLS = 20; 
        canvas.width = COLS * TILE_SIZE;
        canvas.height = ROWS * TILE_SIZE;
        ctx.imageSmoothingEnabled = false;

        const playerNameDisplay = document.getElementById('player-name-display');
        const playerPositionDisplay = document.getElementById('player-position-display');
        const xpDisplay = document.getElementById('xp-display');
        const goldDisplay = document.getElementById('gold-display');
        const messageLog = document.getElementById('message-log');
        const interactionPrompt = document.getElementById('interaction-prompt');
        const castleTooltip = document.getElementById('castle-tooltip'); 
        const inventoryItemsDisplay = document.getElementById('inventory-items-display');
        const overdueTasksListDiv = document.getElementById('overdue-tasks-list'); 
        const resetGameButton = document.getElementById('resetGameButton');
        const shopHudButton = document.getElementById('shop-hud-button'); 
        
        const userSetupModal = document.getElementById('userSetupModal');
        const completeSetupButton = document.getElementById('completeSetupButton');
        const playerNameInput = document.getElementById('playerName');
        const playerPositionInput = document.getElementById('playerPosition');

        const welcomeModal = document.getElementById('welcomeModal');
        const startAdventureButton = document.getElementById('startAdventureButton');
        const extendDueDateModal = document.getElementById('extendDueDateModal');
        const extendableTasksListDiv = document.getElementById('extendable-tasks-list');
        const closeExtendDueDateModalButton = document.getElementById('closeExtendDueDateModalButton');

        const personalDetailsModal = document.getElementById('personalDetailsModal');
        const playerLikesTextarea = document.getElementById('playerLikes');
        const playerAspirationsTextarea = document.getElementById('playerAspirations');
        const playerPersonalMissionTextarea = document.getElementById('playerPersonalMission');
        const playerStoryTextarea = document.getElementById('playerStory');
        const savePersonalDetailsButton = document.getElementById('savePersonalDetailsButton');
        const closePersonalDetailsButton = document.getElementById('closePersonalDetailsButton');

        const taskModal = document.getElementById('taskModal');
        const addTaskButton = document.getElementById('addTaskButton');
        const missionTypeSelect = document.getElementById('missionType');
        const trabajoFieldsDiv = document.getElementById('trabajoFields');
        const personalFieldsDiv = document.getElementById('personalFields');
        const taskDescriptionInput = document.getElementById('taskDescription'); 
        const requiredClassSelect = document.getElementById('requiredClass'); 
        const taskDifficultyInput = document.getElementById('taskDifficulty'); 
        const personalTaskTitleInput = document.getElementById('personalTaskTitle'); 
        const currentStateTextarea = document.getElementById('currentState');
        const idealStateTextarea = document.getElementById('idealState');
        const stepsToAchieveTextarea = document.getElementById('stepsToAchieve');
        const taskDueDateInput = document.getElementById('taskDueDate');
        const taskListDiv = document.getElementById('task-list');
        const closeTaskModalButton = document.getElementById('closeTaskModalButton');

        const shopModal = document.getElementById('shopModal');
        const shopTitleElement = document.getElementById('shopTitleElement'); 
        const closeShopButton = document.getElementById('closeShopButton');
        const shopItemsContainer = document.getElementById('shop-items');

        let player = {
            x: TILE_SIZE * (Math.floor(COLS / 2) + 2), 
            y: TILE_SIZE * (Math.floor(ROWS / 2) + 2), 
            width: TILE_SIZE, height: TILE_SIZE,
            color: '#FF8C00', name: '', position: '', 
            xp: 0, gold: 0, inventory: [], tasks: [],
            likes: "", aspirations: "", personalMission: "", story: ""
        };
        
        const taskDifficulties = { 
            "Simple": { gold: 0.5, xp: 5, iconShape: 'circle', quadrant: 'bottom-right', order: 0 }, 
            "Complicado": { gold: 1, xp: 10, iconShape: 'square', quadrant: 'top-right', order: 1 }, 
            "Complejo": { gold: 2, xp: 20, iconShape: 'diamond', quadrant: 'top-left', order: 2 }, 
            "Caotico": { gold: 5, xp: 50, iconShape: 'star', quadrant: 'bottom-left', order: 3 }
        };
        
        const requiredClassColors = {
            "Guerrero": "#FF0000", "Mago": "#0000FF", "Paladin": "#FFFF00", "Bardo": "#FFA500"
        };
        const personalCrusadeCategory = { gold: 0, xp: 25, iconShape: 'scroll', iconColor: '#DEB887', quadrant: 'any', order: 4 };

        const castleWidthTiles = 2.5; 
        const castleHeightTiles = 2.5; 
        const castleX = (COLS / 2 - castleWidthTiles / 2) * TILE_SIZE; 
        const castleY = (ROWS / 2 - castleHeightTiles / 2) * TILE_SIZE; 
        
        const worldObjects = [
            { id: 'task_terminal', name: 'Castillo de Edictos', x: castleX, y: castleY, width: castleWidthTiles * TILE_SIZE, height: castleHeightTiles * TILE_SIZE, type: 'task_hub' }, 
            { id: 'boundary_top', type: 'wall', x: 0, y: -TILE_SIZE, width: COLS * TILE_SIZE, height: TILE_SIZE }, { id: 'boundary_bottom', type: 'wall', x: 0, y: ROWS * TILE_SIZE, width: COLS * TILE_SIZE, height: TILE_SIZE }, { id: 'boundary_left', type: 'wall', x: -TILE_SIZE, y: 0, width: TILE_SIZE, height: ROWS * TILE_SIZE }, { id: 'boundary_right', type: 'wall', x: COLS * TILE_SIZE, y: 0, width: TILE_SIZE, height: ROWS * TILE_SIZE },
        ];

        const shopDefinition = {
            items: [
                { id: 'life_potion', name: 'Poción de Vida', price: 20, description: 'Otorga 3 días más para un edicto de trabajo.', effect: {type: 'extend_due_date'}, message: "¡Plazo extendido! El edicto ahora tiene más tiempo." },
                { id: 'spectral_aid_spell', name: 'Hechizo de Ayuda Espectral', price: 75, description: 'Invoca ayuda mística para un edicto complicado.', effect: {type: 'xp', value: 50}, message: "¡Los espectros te asisten! Ganas 50 XP por su sabiduría." },
                { id: 'heroes_feast', name: 'Comida de Héroes', price: 150, description: '¡Te ganas una comida pagada por el Rey!', effect: {type: 'gold', value: 30}, message: "¡El Rey invita! Recibes 30 de Oro como viáticos." },
                { id: 'elven_shores_pass', name: 'Pase a las Playas Élficas', price: 250, description: '¡Un merecido descanso! Pide una tarde libre.', effect: {type: 'xp', value: 100}, message: "¡Descanso élfico concedido! Regresas renovado. +100 XP." }
            ]
        };
        const initialPotion = { id: 'life_potion', name: 'Poción de Vida', description: 'Otorga 3 días más para un edicto de trabajo.', effect: {type: 'extend_due_date'}, message: "¡Plazo extendido!" };


        let currentInteractable = null; let isModalOpen = false; let gameInitialized = false;
        const GAME_STORAGE_KEY = 'officeQuestGameState_v12_localStorage'; // Nueva versión por cambios en tienda

        missionTypeSelect.addEventListener('change', () => {
            if (missionTypeSelect.value === 'Personal') {
                trabajoFieldsDiv.classList.add('hidden-fields');
                personalFieldsDiv.classList.remove('hidden-fields');
            } else { 
                trabajoFieldsDiv.classList.remove('hidden-fields');
                personalFieldsDiv.classList.add('hidden-fields');
            }
        });

        function saveGameState() { try { localStorage.setItem(GAME_STORAGE_KEY, JSON.stringify(player)); } catch (e) { console.error("Error saving game state to localStorage:", e); logMessage("Error al guardar.", "error"); } }
        function loadGameState() { 
            try { 
                const savedData = localStorage.getItem(GAME_STORAGE_KEY); 
                if (savedData) { 
                    const loadedPlayer = JSON.parse(savedData); 
                    player = { ...player, ...loadedPlayer, inventory: loadedPlayer.inventory || [], tasks: loadedPlayer.tasks || [] }; 
                    gameInitialized = true; isModalOpen = false; userSetupModal.style.display = 'none'; 
                    updateUI(); 
                    logMessage(`¡Bienvenido de nuevo, ${player.name}! Tu gesta continúa.`, "info"); 
                } else { 
                    userSetupModal.style.display = 'flex'; isModalOpen = true; 
                    logMessage("¡Saludos, aventurero! Completa tu registro.", "info"); 
                } 
            } catch (e) { 
                console.error("Error loading game state from localStorage:", e); 
                logMessage("Error al cargar datos guardados. Empezando de nuevo.", "error"); 
                userSetupModal.style.display = 'flex'; isModalOpen = true; 
            } 
        }
        resetGameButton.onclick = () => { if (confirm("¿Estás seguro de que quieres reiniciar el juego? Todo tu progreso guardado se perderá.")) { localStorage.removeItem(GAME_STORAGE_KEY); logMessage("Juego reiniciado. Por favor, actualiza la página.", "info"); window.location.reload(); } };
        
        shopHudButton.addEventListener('click', () => { if (gameInitialized && !isModalOpen) { openShop(); } }); 
        playerPositionDisplay.addEventListener('click', () => { if (gameInitialized && !isModalOpen) { playerLikesTextarea.value = player.likes || ""; playerAspirationsTextarea.value = player.aspirations || ""; playerPersonalMissionTextarea.value = player.personalMission || ""; playerStoryTextarea.value = player.story || ""; personalDetailsModal.style.display = 'flex'; isModalOpen = true; } });
        savePersonalDetailsButton.onclick = () => { player.likes = playerLikesTextarea.value.trim(); player.aspirations = playerAspirationsTextarea.value.trim(); player.personalMission = playerPersonalMissionTextarea.value.trim(); player.story = playerStoryTextarea.value.trim(); saveGameState(); logMessage("Crónica del héroe actualizada.", "reward"); personalDetailsModal.style.display = 'none'; isModalOpen = false; checkInteraction(); };
        closePersonalDetailsButton.onclick = () => { personalDetailsModal.style.display = 'none'; isModalOpen = false; checkInteraction(); };
        
        startAdventureButton.onclick = () => { if (!player.inventory.find(item => item.id === 'life_potion')) { player.inventory.push({...initialPotion}); } updateUI(); saveGameState(); welcomeModal.style.display = 'none'; isModalOpen = false; checkInteraction(); };
        closeExtendDueDateModalButton.onclick = () => { extendDueDateModal.style.display = 'none'; isModalOpen = false; checkInteraction(); };

        function getDaysUntilDue(dueDateString) { if (!dueDateString) return Infinity; const today = new Date(); today.setHours(0, 0, 0, 0); const parts = dueDateString.split('-'); const year = parseInt(parts[0], 10); const month = parseInt(parts[1], 10) - 1; const day = parseInt(parts[2], 10); const dueDate = new Date(year, month, day); dueDate.setHours(0,0,0,0); if (isNaN(dueDate.getTime())) return Infinity; const diffTime = dueDate.getTime() - today.getTime(); return Math.ceil(diffTime / (1000 * 60 * 60 * 24)); }

        function drawPlayer() { const x = player.x; const y = player.y; const size = TILE_SIZE; const pColor = player.color; const outlineColor = '#4A4A4A'; ctx.fillStyle = pColor; ctx.fillRect(x + size * 0.2, y + size * 0.7, size * 0.6, size * 0.2); ctx.fillRect(x + size * 0.3, y + size * 0.3, size * 0.4, size * 0.4); ctx.beginPath(); ctx.moveTo(x + size * 0.5, y + size * 0.3); ctx.lineTo(x + size * 0.7, y + size * 0.1); ctx.lineTo(x + size * 0.5, y + size * 0.1); ctx.closePath(); ctx.fill(); ctx.fillRect(x + size * 0.4, y + size * 0.05, size * 0.2, size * 0.15); ctx.fillRect(x + size * 0.5, y + 0, size * 0.1, size * 0.05); ctx.strokeStyle = outlineColor; ctx.lineWidth = 1; ctx.strokeRect(x + size * 0.2, y + size * 0.7, size * 0.6, size * 0.2); ctx.strokeRect(x + size * 0.3, y + size * 0.3, size * 0.4, size * 0.4); }
        function drawWorldObjects() { worldObjects.forEach(obj => { if (obj.type === 'wall') return; if (obj.id === 'task_terminal') drawCastleTerminal(obj); }); }
        function drawCastleTerminal(obj) { const x = obj.x; const y = obj.y; const width = obj.width; const height = obj.height; const crenellationHeight = TILE_SIZE / 3.5; const towerSectionTopY = y + crenellationHeight; const towerSectionHeight = height * 0.35; const mainWallTopY = towerSectionTopY + towerSectionHeight; const mainWallHeight = height - towerSectionHeight - crenellationHeight; ctx.fillStyle = '#A9A9A9'; ctx.fillRect(x, mainWallTopY, width, mainWallHeight); const towerWidth = width / 3; ctx.fillStyle = '#808080'; ctx.fillRect(x, towerSectionTopY, towerWidth, towerSectionHeight); drawSolidCrenellations(x, towerSectionTopY, towerWidth, crenellationHeight); ctx.fillRect(x + towerWidth, towerSectionTopY, width - 2 * towerWidth, towerSectionHeight); drawSolidCrenellations(x + towerWidth, towerSectionTopY, width - 2 * towerWidth, crenellationHeight); ctx.fillRect(x + width - towerWidth, towerSectionTopY, towerWidth, towerSectionHeight); drawSolidCrenellations(x + width - towerWidth, towerSectionTopY, towerWidth, crenellationHeight); const doorWidth = width / 3.5; const doorHeight = mainWallHeight * 0.6; ctx.fillStyle = '#654321'; ctx.fillRect(x + (width - doorWidth) / 2, mainWallTopY + (mainWallHeight - doorHeight), doorWidth, doorHeight); ctx.fillStyle = '#C0C0C0'; ctx.fillRect(x + (width - doorWidth) / 2 + doorWidth * 0.45, mainWallTopY + mainWallHeight - doorHeight / 2, doorWidth * 0.1, doorHeight * 0.1); const mainVentanaWidth = TILE_SIZE * 0.8; const mainVentanaHeight = TILE_SIZE * 0.6; const mainVentanaX = x + (width - mainVentanaWidth) / 2; const mainVentanaY = mainWallTopY + mainWallHeight * 0.2; ctx.fillStyle = '#5D4037'; ctx.fillRect(mainVentanaX, mainVentanaY, mainVentanaWidth, mainVentanaHeight); ctx.fillStyle = '#ADD8E6'; ctx.fillRect(mainVentanaX + 4, mainVentanaY + 4, mainVentanaWidth - 8, mainVentanaHeight - 8); ctx.fillStyle = '#5D4037'; ctx.fillRect(mainVentanaX + mainVentanaWidth/2 - 2, mainVentanaY + 4, 4, mainVentanaHeight - 8); ctx.fillRect(mainVentanaX + 4, mainVentanaY + mainVentanaHeight/2 - 2, mainVentanaWidth - 8, 4); const smallVentanaSize = TILE_SIZE * 0.25; ctx.fillStyle = '#000000'; ctx.fillRect(x + towerWidth/2 - smallVentanaSize/2, towerSectionTopY + towerSectionHeight * 0.3, smallVentanaSize, smallVentanaSize); ctx.fillRect(x + width - towerWidth + towerWidth/2 - smallVentanaSize/2, towerSectionTopY + towerSectionHeight * 0.3, smallVentanaSize, smallVentanaSize); const flagPoleHeight = TILE_SIZE * 1.2; const flagPoleX = x + width / 2; const flagPoleY = y - flagPoleHeight + crenellationHeight; ctx.fillStyle = '#808080'; ctx.fillRect(flagPoleX - 2, flagPoleY, 4, flagPoleHeight); ctx.fillStyle = '#FF0000'; ctx.beginPath(); ctx.moveTo(flagPoleX + 2, flagPoleY); ctx.lineTo(flagPoleX + 2 + TILE_SIZE * 0.6, flagPoleY + TILE_SIZE * 0.1); ctx.lineTo(flagPoleX + 2, flagPoleY + TILE_SIZE * 0.4); ctx.closePath(); ctx.fill(); }
        function drawSolidCrenellations(x, topOfWallY, wallSegmentWidth, crenellationHeight) { const y = topOfWallY - crenellationHeight; const crenellationBlockWidth = wallSegmentWidth / 5; ctx.fillStyle = '#808080'; for (let i = 0; i < 5; i++) { if (i % 2 === 0) { ctx.fillRect(x + i * crenellationBlockWidth, y, crenellationBlockWidth, crenellationHeight); } } }
        
        let overdueBlinkState = false;
        setInterval(() => { overdueBlinkState = !overdueBlinkState; }, 500); 

        function drawTaskIcons() { 
            player.tasks.forEach(task => { 
                if (!task.completed && task.x !== undefined && task.y !== undefined) { 
                    const centerX = task.x + TILE_SIZE / 2; const centerY = task.y + TILE_SIZE / 2; 
                    let iconShape, iconColor; 
                    if (task.missionType === 'Personal') { 
                        iconShape = personalCrusadeCategory.iconShape; iconColor = personalCrusadeCategory.iconColor; 
                    } else { 
                        const difficultyInfo = taskDifficulties[task.category]; 
                        const classColor = requiredClassColors[task.requiredClass]; 
                        if (!difficultyInfo || !classColor) return; 
                        iconShape = difficultyInfo.iconShape; iconColor = classColor; 
                    } 
                    ctx.fillStyle = iconColor; 
                    switch (iconShape) { 
                        case 'circle': ctx.beginPath(); ctx.arc(centerX, centerY, TILE_SIZE / 3.5, 0, Math.PI * 2); ctx.fill(); break; 
                        case 'square': ctx.fillRect(task.x + TILE_SIZE / 4, task.y + TILE_SIZE / 4, TILE_SIZE / 2, TILE_SIZE / 2); break; 
                        case 'diamond': ctx.beginPath(); ctx.moveTo(centerX, task.y + TILE_SIZE / 6); ctx.lineTo(task.x + TILE_SIZE - TILE_SIZE / 6, centerY); ctx.lineTo(centerX, task.y + TILE_SIZE - TILE_SIZE / 6); ctx.lineTo(task.x + TILE_SIZE / 6, centerY); ctx.closePath(); ctx.fill(); break; 
                        case 'star': const spikes = 5; const outerRadius = TILE_SIZE / 2.8; const innerRadius = TILE_SIZE / 6; let rot = Math.PI / 2 * 3; ctx.beginPath(); for (let i = 0; i < spikes; i++) { let sx = centerX + Math.cos(rot) * outerRadius; let sy = centerY + Math.sin(rot) * outerRadius; ctx.lineTo(sx, sy); rot += Math.PI / spikes; sx = centerX + Math.cos(rot) * innerRadius; sy = centerY + Math.sin(rot) * innerRadius; ctx.lineTo(sx, sy); rot += Math.PI / spikes; } ctx.closePath(); ctx.fill(); ctx.fillStyle = '#FFFFFF'; ctx.font = `${TILE_SIZE / 2.5}px "Press Start 2P"`; ctx.textAlign = 'center'; ctx.textBaseline = 'middle'; ctx.fillText('!', centerX, centerY + 1); break; 
                        case 'scroll': ctx.fillStyle = iconColor; ctx.fillRect(task.x + TILE_SIZE * 0.2, task.y + TILE_SIZE * 0.15, TILE_SIZE * 0.6, TILE_SIZE * 0.7); ctx.fillStyle = '#A0522D'; ctx.fillRect(task.x + TILE_SIZE * 0.2, task.y + TILE_SIZE * 0.15, TILE_SIZE * 0.6, TILE_SIZE * 0.05); ctx.fillRect(task.x + TILE_SIZE * 0.2, task.y + TILE_SIZE * 0.80, TILE_SIZE * 0.6, TILE_SIZE * 0.05); ctx.strokeStyle = '#8B4513'; ctx.lineWidth = 1; ctx.beginPath(); ctx.moveTo(task.x + TILE_SIZE * 0.3, task.y + TILE_SIZE * 0.3); ctx.lineTo(task.x + TILE_SIZE * 0.7, task.y + TILE_SIZE * 0.3); ctx.stroke(); ctx.beginPath(); ctx.moveTo(task.x + TILE_SIZE * 0.3, task.y + TILE_SIZE * 0.5); ctx.lineTo(task.x + TILE_SIZE * 0.7, task.y + TILE_SIZE * 0.5); ctx.stroke(); ctx.beginPath(); ctx.moveTo(task.x + TILE_SIZE * 0.3, task.y + TILE_SIZE * 0.7); ctx.lineTo(task.x + TILE_SIZE * 0.7, task.y + TILE_SIZE * 0.7); ctx.stroke(); break; 
                    } 
                    
                    const daysLeft = getDaysUntilDue(task.dueDate);
                    if (task.missionType === 'Trabajo' && task.dueDate && daysLeft < 0) { 
                        if (overdueBlinkState) {
                            ctx.strokeStyle = 'red';
                            ctx.lineWidth = 2;
                            ctx.strokeRect(task.x + 2, task.y + 2, TILE_SIZE - 4, TILE_SIZE - 4); 
                        }
                    } else if (task.dueDate && task.missionType === 'Trabajo') { 
                        let urgencyColor = null; 
                        if (daysLeft === 0) urgencyColor = '#FF5252'; 
                        else if (daysLeft === 1) urgencyColor = '#FFAB40'; 
                        else if (daysLeft > 1 && daysLeft <= 3) urgencyColor = '#FFFF00'; 
                        if (urgencyColor) { ctx.fillStyle = urgencyColor; ctx.beginPath(); ctx.arc(task.x + TILE_SIZE - 6, task.y + 6, 5, 0, Math.PI * 2); ctx.fill(); ctx.strokeStyle = '#000000'; ctx.lineWidth = 1; ctx.stroke(); } 
                    }
                } 
            }); 
        }
        
        function drawFantasyBackground() { ctx.fillStyle = '#000000'; ctx.fillRect(0, 0, canvas.width, canvas.height); const midColPixel = Math.floor(COLS / 2) * TILE_SIZE; const midRowPixel = Math.floor(ROWS / 2) * TILE_SIZE; const padding = TILE_SIZE * 0.75; const fontSize = TILE_SIZE * 0.45; ctx.fillStyle = '#A0A0A0'; ctx.font = `${fontSize}px "Press Start 2P"`; ctx.textAlign = 'left'; ctx.textBaseline = 'top'; ctx.fillText("Complejo", padding, padding); ctx.textAlign = 'right'; ctx.textBaseline = 'top'; ctx.fillText("Complicado", COLS * TILE_SIZE - padding, padding); ctx.textAlign = 'left'; ctx.textBaseline = 'bottom'; ctx.fillText("Caotico", padding, ROWS * TILE_SIZE - padding); ctx.textAlign = 'right'; ctx.textBaseline = 'bottom'; ctx.fillText("Simple", COLS * TILE_SIZE - padding, ROWS * TILE_SIZE - padding); ctx.strokeStyle = '#FF0000'; ctx.lineWidth = 3; ctx.beginPath(); ctx.moveTo(midColPixel, 0); ctx.lineTo(midColPixel, ROWS * TILE_SIZE); ctx.stroke(); ctx.beginPath(); ctx.moveTo(0, midRowPixel); ctx.lineTo(COLS * TILE_SIZE, midRowPixel); ctx.stroke(); ctx.lineWidth = 1; }

        function updateUI() { playerNameDisplay.textContent = `Nombre: ${player.name || '-'}`; playerPositionDisplay.textContent = `Título: ${player.position || '-'}`; xpDisplay.textContent = `XP: ${player.xp}`; goldDisplay.textContent = `Oro: ${player.gold.toFixed(1)}`; renderInventorySidebar(); renderOverdueTasksSidebar(); }
        function logMessage(msg, type = 'info') { messageLog.textContent = msg; messageLog.style.color = type === 'reward' ? '#AED581' : type === 'error' ? '#EF9A9A' : '#FFFDE7'; }
        function showInteractionPrompt(text) { interactionPrompt.innerHTML = text; interactionPrompt.style.display = 'block'; }
        function hideInteractionPrompt() { interactionPrompt.style.display = 'none'; }
        function renderInventorySidebar() { inventoryItemsDisplay.innerHTML = ''; if (player.inventory.length === 0) { inventoryItemsDisplay.innerHTML = '<p class="text-gray-400 italic text-xs">Vacío...</p>'; return; } player.inventory.forEach((item, index) => { const itemDiv = document.createElement('div'); itemDiv.classList.add('inventory-item'); const itemNameSpan = document.createElement('span'); itemNameSpan.textContent = item.name; itemDiv.appendChild(itemNameSpan); const useButton = document.createElement('button'); useButton.textContent = 'Usar'; useButton.onclick = () => useItem(index); itemDiv.appendChild(useButton); inventoryItemsDisplay.appendChild(itemDiv); }); }
        
        function renderOverdueTasksSidebar() {
            overdueTasksListDiv.innerHTML = '';
            const overdueTasks = player.tasks.filter(task => 
                task.missionType === 'Trabajo' && 
                !task.completed && 
                task.dueDate && 
                getDaysUntilDue(task.dueDate) < 0
            );

            if (overdueTasks.length === 0) {
                overdueTasksListDiv.innerHTML = '<p class="text-gray-400 italic text-xs">¡Ningún edicto vencido!</p>';
                return;
            }

            overdueTasks.sort((a,b) => getDaysUntilDue(a.dueDate) - getDaysUntilDue(b.dueDate)); 

            overdueTasks.forEach(task => {
                const taskDiv = document.createElement('div');
                taskDiv.classList.add('overdue-task-item');
                
                const descP = document.createElement('p');
                descP.classList.add('description');
                descP.textContent = task.description;
                taskDiv.appendChild(descP);

                const daysOverdueP = document.createElement('p');
                daysOverdueP.classList.add('days-overdue');
                daysOverdueP.textContent = `Vencida hace ${Math.abs(getDaysUntilDue(task.dueDate))} día(s).`;
                taskDiv.appendChild(daysOverdueP);
                
                overdueTasksListDiv.appendChild(taskDiv);
            });
        }


        function isColliding(rect1, rect2) { if (!rect1 || !rect2) return false; return rect1.x < rect2.x + rect2.width && rect1.x + rect1.width > rect2.x && rect1.y < rect2.y + rect2.height && rect1.y + rect1.height > rect2.y; }
        
        canvas.addEventListener('click', (event) => {
            if (isModalOpen || !gameInitialized) return;
            const rect = canvas.getBoundingClientRect();
            const clickX = event.clientX - rect.left;
            const clickY = event.clientY - rect.top;
            const castleObj = worldObjects.find(obj => obj.id === 'task_terminal');
            if (castleObj && 
                clickX >= castleObj.x && clickX <= castleObj.x + castleObj.width &&
                clickY >= castleObj.y && clickY <= castleObj.y + castleObj.height) {
                openTaskModal();
            }
        });

        canvas.addEventListener('mousemove', (event) => {
            if (isModalOpen || !gameInitialized) {
                canvas.style.cursor = 'default';
                castleTooltip.style.display = 'none'; 
                return;
            }
            const rect = canvas.getBoundingClientRect();
            const mouseX = event.clientX - rect.left;
            const mouseY = event.clientY - rect.top;

            const castleObj = worldObjects.find(obj => obj.id === 'task_terminal');
            if (castleObj && 
                mouseX >= castleObj.x && mouseX <= castleObj.x + castleObj.width &&
                mouseY >= castleObj.y && mouseY <= castleObj.y + castleObj.height) {
                canvas.style.cursor = 'pointer';
                castleTooltip.style.left = `${castleObj.x + castleObj.width / 2 - castleTooltip.offsetWidth / 2}px`;
                castleTooltip.style.top = `${castleObj.y - castleTooltip.offsetHeight - 5}px`; 
                castleTooltip.style.display = 'block';
            } else {
                canvas.style.cursor = 'default';
                castleTooltip.style.display = 'none';
            }
        });


        function checkInteraction() { 
            if (!gameInitialized || isModalOpen) { hideInteractionPrompt(); currentInteractable = null; return; } 
            currentInteractable = null; hideInteractionPrompt(); 
            const detectionRadius = TILE_SIZE * 0.1; 
            for (const task of player.tasks) { 
                if (!task.completed && task.x !== undefined && task.y !== undefined) { 
                    const taskIconRect = { x: task.x, y: task.y, width: TILE_SIZE, height: TILE_SIZE }; 
                    const playerDetectionRect = { x: player.x - detectionRadius, y: player.y - detectionRadius, width: player.width + detectionRadius * 2, height: player.height + detectionRadius * 2 }; 
                    if (isColliding(playerDetectionRect, taskIconRect)) { 
                        currentInteractable = { type: 'map_task', taskObject: task }; 
                        showInteractionPrompt(`Presiona [E] para acometer:<br>'${(task.missionType === 'Personal' ? task.title : task.description).substring(0,20)}...'`); 
                        return; 
                    } 
                } 
            } 
        }
        function handleInteraction() { if (currentInteractable && !isModalOpen && gameInitialized) { if (currentInteractable.type === 'map_task') completeTask(currentInteractable.taskObject.id); } }

        completeSetupButton.onclick = () => { player.name = playerNameInput.value.trim(); player.position = playerPositionInput.value.trim(); if (!player.name || !player.position) { logMessage("Por favor, completa tu nombre y título.", "error"); return; } userSetupModal.style.display = 'none'; isModalOpen = false; gameInitialized = true; welcomeModal.style.display = 'flex'; isModalOpen = true; updateUI(); logMessage(`¡Bienvenido, ${player.name}! Tu gesta comienza.`, "info"); saveGameState(); checkInteraction(); };

        function getSortedTasks() { let tasksToSort = [...player.tasks]; /* El sort fue eliminado */ return tasksToSort; }
        function getRandomEmptyPosition(categoryOrMissionType, isPersonalCrusade) { 
            let attempts = 0; const MAX_ATTEMPTS = 100; 
            let quadrantSource = isPersonalCrusade ? personalCrusadeCategory : taskDifficulties[categoryOrMissionType];
            if (!quadrantSource) { console.error("Fuente de cuadrante no encontrada para:", categoryOrMissionType, isPersonalCrusade); return null; }
            
            const quadrant = quadrantSource.quadrant;
            const margin = 1; let minCol, maxCol, minRow, maxRow; 
            const midCol = Math.floor(COLS / 2); const midRow = Math.floor(ROWS / 2); 
            
            if (quadrant === 'any') { 
                minCol = margin; maxCol = COLS - 1 - margin;
                minRow = margin; maxRow = ROWS - 1 - margin;
            } else { 
                switch (quadrant) { 
                    case 'top-left': minCol = margin; maxCol = midCol - 1 - margin; minRow = margin; maxRow = midRow - 1 - margin; break; 
                    case 'top-right': minCol = midCol + margin; maxCol = COLS - 1 - margin; minRow = margin; maxRow = midRow - 1 - margin; break; 
                    case 'bottom-left': minCol = margin; maxCol = midCol - 1 - margin; minRow = midRow + margin; maxRow = ROWS - 1 - margin; break; 
                    case 'bottom-right': minCol = midCol + margin; maxCol = COLS - 1 - margin; minRow = midRow + margin; maxRow = ROWS - 1 - margin; break; 
                    default: return null; 
                }
            }
            if (minCol > maxCol || minRow > maxRow) { console.error(`Invalid quadrant dimensions for ${categoryOrMissionType}: C(${minCol}-${maxCol}), R(${minRow}-${maxRow})`); logMessage(`Error de configuración de cuadrante para ${categoryOrMissionType}. No se puede colocar tarea.`, "error"); return null; } 
            while (attempts < MAX_ATTEMPTS) { const randCol = Math.floor(Math.random() * (maxCol - minCol + 1)) + minCol; const randRow = Math.floor(Math.random() * (maxRow - minRow + 1)) + minRow; const posX = randCol * TILE_SIZE; const posY = randRow * TILE_SIZE; const potentialRect = { x: posX, y: posY, width: TILE_SIZE, height: TILE_SIZE }; let collision = false; for (const obj of worldObjects) { if (obj.type !== 'wall' && isColliding(potentialRect, obj)) { collision = true; break; } } if (collision) { attempts++; continue; } for (const existingTask of player.tasks) { if (!existingTask.completed && existingTask.x !== undefined) { const existingIconRect = { x: existingTask.x, y: existingTask.y, width: TILE_SIZE, height: TILE_SIZE }; if (isColliding(potentialRect, existingIconRect)) { collision = true; break; } } } if (collision) { attempts++; continue; } return { x: posX, y: posY }; } 
            logMessage(`No se pudo encontrar espacio para el edicto.`, "error"); return null; 
        }
        function openTaskModal() { isModalOpen = true; /* sortTasksSelect.value = currentSortCriteria; // Eliminado */ missionTypeSelect.value = 'Trabajo'; 
            trabajoFieldsDiv.classList.remove('hidden-fields'); personalFieldsDiv.classList.add('hidden-fields'); 
            renderTaskListInModal(); taskModal.style.display = 'flex'; }
        // Event listener para sortTasksSelect eliminado
        closeTaskModalButton.onclick = () => { taskModal.style.display = 'none'; isModalOpen = false; checkInteraction(); };
        
        addTaskButton.onclick = () => { 
            const missionType = missionTypeSelect.value;
            let newTaskData = {};
            let descriptionForLog; 
            let taskDifficultyForPlacement; 

            if (missionType === 'Personal') {
                const title = personalTaskTitleInput.value.trim();
                if (!title) { logMessage("El título de la Cruzada Personal no puede estar vacío.", "error"); return; }
                newTaskData = {
                    title: title, currentState: currentStateTextarea.value.trim(), idealState: idealStateTextarea.value.trim(), steps: stepsToAchieveTextarea.value.trim(),
                    category: 'Personal', dueDate: '', 
                };
                descriptionForLog = title;
                taskDifficultyForPlacement = 'Personal'; 
            } else { 
                const description = taskDescriptionInput.value.trim();
                if (!description) { logMessage("La descripción del Edicto de Trabajo no puede estar vacía.", "error"); return; }
                const assignedDifficulty = taskDifficultyInput.value; 
                newTaskData = {
                    description: description, requiredClass: requiredClassSelect.value, 
                    category: assignedDifficulty,     
                    dueDate: taskDueDateInput.value,
                };
                descriptionForLog = description;
                taskDifficultyForPlacement = newTaskData.category;
            }
            
            const taskPosition = getRandomEmptyPosition(taskDifficultyForPlacement, missionType === 'Personal'); 
            if (!taskPosition) return; 

            const newTask = { id: `task-${Date.now()}-${Math.random().toString(36).substr(2, 9)}`, missionType, ...newTaskData, completed: false, x: taskPosition.x, y: taskPosition.y, addedTimestamp: Date.now() }; 
            player.tasks.push(newTask); 
            logMessage(`Edicto "${descriptionForLog}" (${missionType}) publicado.`, "reward"); 
            taskDescriptionInput.value = ''; personalTaskTitleInput.value = ''; currentStateTextarea.value = ''; idealStateTextarea.value = ''; stepsToAchieveTextarea.value = ''; taskDueDateInput.value = ''; 
            renderTaskListInModal(); 
            updateUI(); 
            saveGameState(); 
        };
        function renderTaskListInModal() { taskListDiv.innerHTML = ''; const tasksToDisplay = player.tasks; /* Ya no se ordena aquí */ if (tasksToDisplay.length === 0) { taskListDiv.innerHTML = '<p class="text-gray-400 italic">No hay edictos publicados.</p>'; return; } tasksToDisplay.forEach(task => { const taskDiv = document.createElement('div'); taskDiv.classList.add('task-item'); if (task.completed) taskDiv.classList.add('task-completed'); const taskInfoDiv = document.createElement('div'); taskInfoDiv.classList.add('task-info'); const taskDescP = document.createElement('p'); let taskText = `(${task.missionType}) ${task.missionType === 'Personal' ? task.title : task.description}`; if(task.missionType === 'Trabajo') { taskText += ` <span class="task-details">(Requiere: ${task.requiredClass}, Dificultad: ${task.category})</span>`; } else { taskText += ` <span class="task-details">(Cruzada)</span>`;} if (!task.completed && task.x !== undefined) taskText += ` <span class="text-yellow-400 text-xs">[EN MAPA]</span>`; taskDescP.innerHTML = taskText; taskInfoDiv.appendChild(taskDescP); if (task.dueDate && task.missionType === 'Trabajo') { const daysLeft = getDaysUntilDue(task.dueDate); const dueDateTextSpan = document.createElement('span'); dueDateTextSpan.classList.add('due-date-text'); let urgencyText = `Plazo: ${task.dueDate}`; if (daysLeft < 0) urgencyText += ` <span class="overdue">(Vencida)</span>`; else if (daysLeft === 0) urgencyText += ` <span class="due-today">(¡Hoy!)</span>`; else if (daysLeft === 1) urgencyText += ` <span class="due-tomorrow">(Mañana)</span>`; else if (daysLeft <= 3) urgencyText += ` <span class="due-soon">(Próximo)</span>`; dueDateTextSpan.innerHTML = urgencyText; taskInfoDiv.appendChild(dueDateTextSpan); } const completeButton = document.createElement('button'); completeButton.textContent = task.completed ? 'Cumplido' : 'Completar'; completeButton.disabled = task.completed; if (task.completed) { completeButton.style.backgroundColor = '#4CAF50'; completeButton.style.borderColor = '#66BB6A'; } else { completeButton.onclick = () => completeTask(task.id); } taskDiv.appendChild(taskInfoDiv); taskDiv.appendChild(completeButton); taskListDiv.appendChild(taskDiv); }); }
        function completeTask(taskId) { 
            const task = player.tasks.find(t => t.id === taskId); 
            if (task && !task.completed) { 
                task.completed = true; 
                let rewards;
                if (task.missionType === 'Personal') { rewards = personalCrusadeCategory; } 
                else { rewards = taskDifficulties[task.category]; }
                if (rewards) { player.gold += rewards.gold; player.xp += rewards.xp; updateUI(); logMessage(`¡"${task.missionType === 'Personal' ? task.title : task.description}" cumplido! Ganaste ${rewards.gold} Oro y ${rewards.xp} XP.`, 'reward'); } 
                if (isModalOpen && taskModal.style.display === 'flex') renderTaskListInModal(); 
                currentInteractable = null; hideInteractionPrompt(); saveGameState(); checkInteraction(); 
            } 
        }

        function openShop() { isModalOpen = true; shopTitleElement.textContent = "Emporio del Aventurero"; shopItemsContainer.innerHTML = ''; shopDefinition.items.forEach(item => { const itemDiv = document.createElement('div'); itemDiv.classList.add('shop-item', 'my-2', 'p-2', 'border', 'border-gray-700', 'rounded'); const itemName = document.createElement('p'); itemName.textContent = `${item.name} - ${item.price} Oro`; itemDiv.appendChild(itemName); const itemDesc = document.createElement('p'); itemDesc.textContent = item.description; itemDesc.classList.add('text-xs', 'text-blue-grey-200'); itemDiv.appendChild(itemDesc); const buyButton = document.createElement('button'); buyButton.textContent = 'Comprar'; buyButton.onclick = () => buyItem(item); if (player.gold < item.price) buyButton.disabled = true; itemDiv.appendChild(buyButton); shopItemsContainer.appendChild(itemDiv); }); shopModal.style.display = 'flex'; }
        function buyItem(item) { if (player.gold >= item.price) { player.gold -= item.price; player.inventory.push({...item}); updateUI(); logMessage(`¡Adquiriste '${item.name}' por ${item.price} Oro!`, 'reward'); saveGameState(); openShop(); } else { logMessage('No tienes suficiente Oro para este artículo.', 'error'); } }
        
        function useItem(itemIndexInInventory) {
            if (itemIndexInInventory < 0 || itemIndexInInventory >= player.inventory.length) { return; }
            const itemToUse = player.inventory[itemIndexInInventory];

            if (itemToUse.id === 'extend_potion' || itemToUse.id === 'life_potion') { // Acepta ambos IDs por si acaso
                const extendableTasks = player.tasks.filter(task => task.missionType === 'Trabajo' && !task.completed && task.dueDate);
                if (extendableTasks.length === 0) {
                    logMessage("No hay edictos de trabajo con plazo para extender.", "info");
                    return; 
                }
                extendableTasksListDiv.innerHTML = '';
                extendableTasks.forEach(task => {
                    const taskDiv = document.createElement('div');
                    taskDiv.classList.add('extend-due-date-item');
                    taskDiv.textContent = `${task.description} (Plazo: ${task.dueDate})`;
                    taskDiv.onclick = () => {
                        const originalDate = new Date(task.dueDate + "T00:00:00"); 
                        originalDate.setDate(originalDate.getDate() + 3);
                        task.dueDate = originalDate.toISOString().split('T')[0];
                        
                        player.inventory.splice(itemIndexInInventory, 1);
                        logMessage(`¡Plazo de "${task.description}" extendido 3 días!`, 'reward');
                        updateUI();
                        saveGameState();
                        extendDueDateModal.style.display = 'none';
                        isModalOpen = false;
                        checkInteraction();
                        if (isModalOpen && taskModal.style.display === 'flex') renderTaskListInModal(); 
                    };
                    extendableTasksListDiv.appendChild(taskDiv);
                });
                extendDueDateModal.style.display = 'flex';
                isModalOpen = true;
                return; 
            }

            if (itemToUse && itemToUse.effect) { 
                if (itemToUse.effect.type === 'xp') player.xp += itemToUse.effect.value; 
                else if (itemToUse.effect.type === 'gold') player.gold += itemToUse.effect.value; 
                logMessage(itemToUse.message || `¡Usaste ${itemToUse.name}!`, 'reward'); 
            } else { 
                logMessage(`No se pudo usar ${itemToUse.name}. Efecto no definido.`, 'error'); 
            } 
            player.inventory.splice(itemIndexInInventory, 1); 
            updateUI(); saveGameState(); 
        }
        closeShopButton.onclick = () => { shopModal.style.display = 'none'; isModalOpen = false; checkInteraction(); };
        
        function handleKeyDown(e) { if (isModalOpen || !gameInitialized) return; let newX = player.x; let newY = player.y; switch(e.key) { case 'ArrowUp': case 'w': newY -= TILE_SIZE; break; case 'ArrowDown': case 's': newY += TILE_SIZE; break; case 'ArrowLeft': case 'a': newX -= TILE_SIZE; break; case 'ArrowRight': case 'd': newX += TILE_SIZE; break; case 'e': case 'E': handleInteraction(); return; } const minCanvasX = 0; const maxCanvasX = (COLS - 1) * TILE_SIZE; const minCanvasY = 0; const maxCanvasY = (ROWS - 1) * TILE_SIZE; newX = Math.max(minCanvasX, Math.min(newX, maxCanvasX)); newY = Math.max(minCanvasY, Math.min(newY, maxCanvasY)); let intendedMove = { x: newX, y: newY, width: player.width, height: player.height }; let canMove = true; for (const obj of worldObjects) { if (obj.type !== 'wall' && isColliding(intendedMove, obj)) { if (obj.type === 'task_hub') { canMove = false; break; } } } if (canMove) { player.x = newX; player.y = newY; } checkInteraction(); }
        window.addEventListener('keydown', handleKeyDown);

        document.getElementById('btn-up').addEventListener('click', () => handleKeyDown({ key: 'ArrowUp' })); document.getElementById('btn-down').addEventListener('click', () => handleKeyDown({ key: 'ArrowDown' })); document.getElementById('btn-left').addEventListener('click', () => handleKeyDown({ key: 'ArrowLeft' })); document.getElementById('btn-right').addEventListener('click', () => handleKeyDown({ key: 'ArrowRight' })); document.getElementById('btn-action').addEventListener('click', () => handleKeyDown({ key: 'e' }));

        function gameLoop() { ctx.clearRect(0, 0, canvas.width, canvas.height); drawFantasyBackground(); drawWorldObjects(); drawTaskIcons(); if (gameInitialized) { drawPlayer(); } requestAnimationFrame(gameLoop); }

        loadGameState(); 
        gameLoop();
    </script>
</body>
</html>
