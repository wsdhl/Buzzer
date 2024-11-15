<[![Netlify Status](https://api.netlify.com/api/v1/badges/389f5b2b-b2de-4434-bc36-8fb0d98a7b98/deploy-status)](https://app.netlify.com/sites/buzzergame/deploys)>
<html lang="de">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Buzzersystem</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 0;
            padding: 0;
        }

        .container {
            max-width: 600px;
            margin: 50px auto;
        }

        .user-list {
            margin-top: 20px;
        }

        .user {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin: 10px 0;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
        }

        .user.buzzed {
            background-color: #ffeb3b;
        }

        button {
            padding: 10px 15px;
            font-size: 16px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        button:disabled {
            background-color: #ccc;
            cursor: not-allowed;
        }

        #reset-button {
            margin-top: 20px;
            background-color: #f44336;
            color: white;
        }

        #admin-container {
            display: none;
            margin-top: 20px;
        }

        #questions-container {
            display: none;
            margin-top: 20px;
        }

        .error {
            color: red;
        }
    </style>
</head>

<body>
    <div class="container">
        <h1>Buzzersystem</h1>

        <!-- Admin Login -->
        <div id="admin-login">
            <h2>Admin Login</h2>
            <input type="password" id="admin-password" placeholder="Admin Passwort" />
            <button id="admin-login-button">Login</button>
            <p class="error" id="login-error"></p>
        </div>

        <!-- Admin Dashboard -->
        <div id="admin-container">
            <h2>Admin Dashboard</h2>
            <button id="switch-game-mode">Wechsel zu Schätzfragen</button>
            <button id="start-game" disabled>Spiel starten</button>
            <button id="reset-button" disabled>Buzzer zurücksetzen</button>
            <button id="reveal-answers" disabled>Antworten anzeigen</button>
        </div>

        <!-- Benutzer Anmelden -->
        <div id="user-container">
            <h3>Benutzer anmelden</h3>
            <input type="text" id="username" placeholder="Benutzername eingeben" />
            <button id="add-user">Benutzer hinzufügen</button>
            <div class="user-list" id="user-list"></div>
        </div>

        <!-- Fragen und Antwortmöglichkeiten (nur im Schätzfragen-Modus sichtbar) -->
        <div id="questions-container">
            <h2>Schätzfragen</h2>
        </div>
    </div>

    <script>
        const adminPassword = "admin123"; // Admin Passwort
        let isAdmin = false;
        let gameMode = 'buzzing'; // Aktueller Spielmodus
        let users = [];
        let answersRevealed = false;

        // DOM-Elemente
        const userList = document.getElementById('user-list');
        const usernameInput = document.getElementById('username');
        const addUserButton = document.getElementById('add-user');
        const adminLoginButton = document.getElementById('admin-login-button');
        const adminPasswordInput = document.getElementById('admin-password');
        const adminContainer = document.getElementById('admin-container');
        const adminLoginSection = document.getElementById('admin-login');
        const loginError = document.getElementById('login-error');
        const switchGameModeButton = document.getElementById('switch-game-mode');
        const revealAnswersButton = document.getElementById('reveal-answers');
        const questionsContainer = document.getElementById('questions-container');

        // Admin Login
        adminLoginButton.addEventListener('click', () => {
            const password = adminPasswordInput.value.trim();
            if (password === adminPassword) {
                isAdmin = true;
                adminLoginSection.style.display = 'none';
                adminContainer.style.display = 'block';
                loginError.textContent = '';
                renderUsers();
            } else {
                loginError.textContent = 'Falsches Passwort! Bitte versuche es erneut.';
            }
        });

        // Benutzer hinzufügen
        addUserButton.addEventListener('click', () => {
            const username = usernameInput.value.trim();
            if (username && !users.find(user => user.name === username)) {
                users.push({ name: username, answer: '', answerRevealed: false });
                renderUsers();
                usernameInput.value = ''; // Eingabefeld leeren
            } else {
                alert('Benutzername ist leer oder bereits vorhanden!');
            }
        });

        // Benutzerliste rendern
        function renderUsers() {
            userList.innerHTML = '';
            users.forEach((user, index) => {
                const userDiv = document.createElement('div');
                userDiv.className = 'user';
                userDiv.innerHTML = `
                    <span>${user.name}</span>
                    ${gameMode === 'questions' ? 
                        `<input type="text" placeholder="Antwort eingeben" 
                        ${answersRevealed ? 'disabled' : ''} 
                        oninput="updateAnswer(${index}, this.value)" />`
                        : ''}
                    <span>${answersRevealed ? user.answer : ''}</span>
                `;
                userList.appendChild(userDiv);
            });
        }

        // Antwort eines Benutzers aktualisieren
        window.updateAnswer = (index, value) => {
            users[index].answer = value;
        };

        // Spielmodus wechseln
        switchGameModeButton.addEventListener('click', () => {
            gameMode = gameMode === 'buzzing' ? 'questions' : 'buzzing';
            switchGameModeButton.textContent = gameMode === 'buzzing' ? 'Wechsel zu Schätzfragen' : 'Wechsel zu Buzzern';
            questionsContainer.style.display = gameMode === 'questions' ? 'block' : 'none';
            revealAnswersButton.disabled = gameMode === 'buzzing';
            renderUsers();
        });

        // Antworten anzeigen
        revealAnswersButton.addEventListener('click', () => {
            answersRevealed = true;
            renderUsers();
        });
    </script>
</body>

</html>
