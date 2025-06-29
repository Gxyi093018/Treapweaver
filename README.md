<script type="text/javascript">
        var gk_isXlsx = false;
        var gk_xlsxFileLookup = {};
        var gk_fileData = {};
        function filledCell(cell) {
          return cell !== '' && cell != null;
        }
        function loadFileData(filename) {
        if (gk_isXlsx && gk_xlsxFileLookup[filename]) {
            try {
                var workbook = XLSX.read(gk_fileData[filename], { type: 'base64' });
                var firstSheetName = workbook.SheetNames[0];
                var worksheet = workbook.Sheets[firstSheetName];

                // Convert sheet to JSON to filter blank rows
                var jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1, blankrows: false, defval: '' });
                // Filter out blank rows (rows where all cells are empty, null, or undefined)
                var filteredData = jsonData.filter(row => row.some(filledCell));

                // Heuristic to find the header row by ignoring rows with fewer filled cells than the next row
                var headerRowIndex = filteredData.findIndex((row, index) =>
                  row.filter(filledCell).length >= filteredData[index + 1]?.filter(filledCell).length
                );
                // Fallback
                if (headerRowIndex === -1 || headerRowIndex > 25) {
                  headerRowIndex = 0;
                }

                // Convert filtered JSON back to CSV
                var csv = XLSX.utils.aoa_to_sheet(filteredData.slice(headerRowIndex)); // Create a new sheet from filtered array of arrays
                csv = XLSX.utils.sheet_to_csv(csv, { header: 1 });
                return csv;
            } catch (e) {
                console.error(e);
                return "";
            }
        }
        return gk_fileData[filename] || "";
        }
        </script><!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TripWeaver App</title>
    <style>
        body {
           font-family: Arial, sans-serif;
            background-color: #f0f9ff;
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow-y: auto;
            touch-action: manipulation;
        }
        .container {
            width: 300px;
            padding: 20px;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            text-align: center;
            margin: 0;
        }
        .logo {
            margin-bottom: 20px;
        }
        .logo img {
            width: 120px;
            height: auto;
        }
        header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding: 10px 0;
        }
        header .back, header .menu {
            font-size: 24px;
            padding: 8px;
            cursor: pointer;
            color: #1b263b;
            touch-action: manipulation;
        }
        header h1 {
            font-size: 20px;
            color: #1b263b;
            margin: 0;
            font-weight: 600;
        }
        h2 {
            color: #1b263b;
            font-size: 24px;
            margin-bottom: 10px;
        }
        p {
            color: #6b7280;
            font-size: 14px;
            margin-bottom: 20px;
        }
        .form-group {
            margin-bottom: 15px;
            text-align: left;
        }
        .form-group label {
            display: block;
            font-size: 14px;
            color: #1b263b;
            margin-bottom: 5px;
        }
        .form-group input {
            width: 100%;
            padding: 10px;
            border: 1px solid #bfdbfe;
            border-radius: 25px;
            box-sizing: border-box;
            font-size: 14px;
        }
        .form-group input:focus {
            outline: none;
            border-color: #1b263b;
        }
        button {
            width: 100%;
            padding: 12px;
            background-color: #0021fb; 
            color: white;
            border: none;
            border-radius: 25px;
            font-size: 16px;
            cursor: pointer;
            margin-bottom: 15px;
        }
         button:hover {
            background-color: #0021fb;
        }
         .social-btn {
            width: 100%;
            padding: 10px;
            border: 1px solid #bfdbfe;
            border-radius: 25px;
            background-color: white;
            color: #1b263b;
            font-size: 14px;
            cursor: pointer;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .social-btn img {
            width: 20px;
            height: 20px;
            margin-right: 10px;
        }
        .social-btn:hover {
            background-color: #f0f9ff;
        }
        .signup-link {
            color: #0021fb;
            font-size: 14px;
            text-decoration: none;
        }
        .signup-link:hover {
            text-decoration: underline;
        }
        .page {
            display: none;
        }
        .page.active {
            display: block;
        }
        /* Login Page Styles */
        .login-welcome {
            text-align: center;
            margin-bottom: 20px;
        }
        .login-welcome h1 {
            font-size: 24px;
            color: #1b263b;
        }
        .login-form {
            text-align: center;
        }
        .login-form input {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
            border: 1px solid #bfdbfe;
            border-radius: 5px;
            font-size: 14px;
        }
        .login-form button {
            width: 100%;
            padding: 12px;
            background-color: #b2ebf2;
            color: #1b263b;
            border: none;
            border-radius: 25px;
            font-size: 16px;
            cursor: pointer;
            touch-action: manipulation;
        }
        .login-form button:active {
            background-color: #e0f7fa;
        }
        .social-login {
            text-align: center;
            margin-top: 20px;
        }
        .social-login button {
             width: 100%;
            padding: 10px;
            border: 1px solid #bfdbfe;
            border-radius: 25px;
            background-color: white;
            color: #1b263b;
            font-size: 14px;
            cursor: pointer;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .social-login button:active {
            background-color: #eff6ff;
        }
        .signup-link {
            color: #0021fb;
            font-size: 14px;
            text-decoration: none;
        }
        .signup-link:hover {
            text-decoration: underline;
        }
        /* Index Page Styles */
        .user-info {
            text-align: center;
            margin-bottom: 20px;
        }
        .user-info h2 {
            font-size: 20px;
            color: #1b263b;
            margin-bottom: 5px;
        }
        .user-info p {
            font-size: 14px;
            color: #4a5568;
        }
        .menu-list {
            list-style: none;
            padding: 0;
        }
        .menu-list li {
            padding: 10px;
            background-color: #eff6ff;
            margin-bottom: 5px;
            border-radius: 5px;
            font-size: 16px;
            color: #1b263b;
            cursor: pointer;
            display: flex;
            justify-content: space-between;
            align-items: center;
            touch-action: manipulation;
        }
        .menu-list li:active {
            background-color: #d1d5db;
        }
        /* Trip Page Styles */
        .location-section {
            margin-bottom: 20px;
        }
        .locations {
            display: flex;
            overflow-x: auto;
            gap: 10px;
            padding-bottom: 10px;
            border-bottom: 1px solid #bfdbfe;
            -webkit-overflow-scrolling: touch;
            scroll-behavior: smooth;
            padding-left: 10px;
            touch-action: pan-x;
        }
        .location {
            min-width: 60px;
            height: 90px;
            border: 2px dashed #bfdbfe;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            border-radius: 5px;
            cursor: pointer;
            overflow: hidden;
            touch-action: manipulation;
        }
        .location img {
            width: 100%;
            height: 60px;
            object-fit: cover;
        }
        .location .title {
            font-size: 12px;
            color: #1b263b;
            text-align: center;
            padding: 2px 4px;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
            max-width: 100%;
        }
        .location:active {
            background-color: #eff6ff;
        }
        button.add-plan {
            width: 100%;
            padding: 12px;
            background-color: #e5e7eb;
            color: #1b263b;
            border: none;
            border-radius: 25px;
            font-size: 16px;
            cursor: pointer;
            margin-bottom: 20px;
            min-height: 48px;
            touch-action: manipulation;
        }
        button.add-plan:active {
            background-color: #d1d5db;
        }
        .plan-list {
            text-align: left;
        }
        .plan-list h3 {
            font-size: 16px;
            color: #1b263b;
            margin-bottom: 10px;
        }
        .plan-list p {
            margin: 5px 0;
            padding: 8px;
            background-color: #eff6ff;
            border-radius: 5px;
            font-size: 14px;
        }
        .plan-list .total {
            font-size: 12px;
            color: #6b7280;
            margin-top: 10px;
        }
        @media (max-width: 360px) {
            .container { padding: 15px; }
            .logo img { width: 100px; }
            .login-form input { font-size: 12px; }
            .login-form button { font-size: 14px; }
            .menu-list li { font-size: 14px; }
            .location { min-width: 50px; height: 75px; }
            .location img { height: 50px; }
            .location .title { font-size: 10px; }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="logo">
             <img src="my-logo.png.png">
        </div>

        <div id="login-page" class="page active">
        <div class="login-welcome">
            <h1>Welcome</h1>
            <p>Fill out the information below in order to access your account.</p>
        </div>
        <form id="loginForm">
            <div class="form-group">
                <label for="email">Email</label>
                <input type="email" placeholder="Enter your email" required>
                <label for="password">Password</label>
                <input type="password" placeholder="Enter your password" required>
                <button  onclick="login()">Sign In</button>
            </div>
        <p>Or sign in with</p>
        <button class="social-btn">
            <img src="https://www.google.com/favicon.ico" alt="Google Icon"> Continue with Google
        </button>
        <button class="social-btn">
            <img src="https://www.apple.com/favicon.ico" alt="Apple Icon"> Continue with Apple
        </button>
        <p>Don't have an account? <a href="signup.html" class="signup-link">Sign Up here</a></p>
        </div>
    </div>

        <div id="index-page" class="page">
            <header>
                <span class="back">←</span>
                <h1>Home</h1>
                <span class="menu">…</span>
            </header>
            <div class="user-info">
                <h2>Alex Campbell</h2>
                <p>alex.campbell@example.com</p>
            </div>
            <ul class="menu-list">
                <li onclick="showPage('trip')"><span>My Trips</span><span>></span></li>
                <li><span>Saved Places</span><span>></span></li>
                <li><span>Recommendations</span><span>></span></li>
                <li><span>Notifications</span><span>></span></li>
                <li><span>Profile</span><span>></span></li>
                <li><span>Settings</span><span>></span></li>
                <li><span>Help</span><span>></span></li>
                <li><span>About</span><span>></span></li>
                <li><span>Log out</span><span>></span></li>
            </ul>
        </div>

        <div id="trip-page" class="page">
            <header>
                <span class="back" onclick="showPage('index')">←</span>
                <h1>Tokyo Trip</h1>
                <span class="menu">…</span>
            </header>
            <h2>Plan Your Journey</h2>
            <div class="location-section">
                <div class="locations">
                    <div class="location" onclick="addToPlan('Shibuya Crossing')">
                        <img src="https://via.placeholder.com/60" alt="Shibuya Crossing">
                        <span class="title">Shibuya Crossing</span>
                    </div>
                    <div class="location" onclick="addToPlan('Tokyo Tower')">
                        <img src="https://via.placeholder.com/60" alt="Tokyo Tower">
                        <span class="title">Tokyo Tower</span>
                    </div>
                    <div class="location" onclick="addToPlan('Akihabara')">
                        <img src="https://via.placeholder.com/60" alt="Akihabara">
                        <span class="title">Akihabara</span>
                    </div>
                    <div class="location" onclick="addToPlan('Asakusa Temple')">
                        <img src="https://via.placeholder.com/60" alt="Asakusa Temple">
                        <span class="title">Asakusa Temple</span>
                    </div>
                    <div class="location" onclick="addToPlan('Shinjuku Gyoen')">
                        <img src="https://via.placeholder.com/60" alt="Shinjuku Gyoen">
                        <span class="title">Shinjuku Gyoen</span>
                    </div>
                    <div class="location" onclick="addToPlan('Meiji Shrine')">
                        <img src="https://via.placeholder.com/60" alt="Meiji Shrine">
                        <span class="title">Meiji Shrine</span>
                    </div>
                    <div class="location" onclick="addToPlan('Odaiba')">
                        <img src="https://via.placeholder.com/60" alt="Odaiba">
                        <span class="title">Odaiba</span>
                    </div>
                    <div class="location" onclick="addToPlan('Harajuku')">
                        <img src="https://via.placeholder.com/60" alt="Harajuku">
                        <span class="title">Harajuku</span>
                    </div>
                </div>
            </div>
            <button class="add-plan" onclick="addToPlanConfirm()">Add to Plan</button>
            <div class="plan-list" id="planList">
                <h3>Your Plan</h3>
                <p>Shibuya Crossing (Added)</p>
                <p>Tokyo Tower (Added)</p>
                <p>Akihabara (Added)</p>
                <p>Asakusa Temple (Added)</p>
                <p class="total">Total: 4 places</p>
            </div>
        </div>
    </div>

    <script>
        function login() {
            const email = document.querySelector('#login-page input[type="email"]').value;
            const password = document.querySelector('#login-page input[type="password"]').value;
            if (email && password) {
                showPage('index');
            } else {
                alert('Please enter email and password!');
            }
        }

        let selectedPlaces = ['Shibuya Crossing', 'Tokyo Tower', 'Akihabara', 'Asakusa Temple'];

        function addToPlan(place) {
            if (!selectedPlaces.includes(place)) {
                selectedPlaces.push(place);
                updatePlanList();
            }
        }

        function addToPlanConfirm() {
            if (selectedPlaces.length > 0) {
                alert('Places added to your plan: ' + selectedPlaces.join(', '));
                selectedPlaces = [];
                updatePlanList();
            } else {
                alert('No places selected to add!');
            }
        }

        function updatePlanList() {
            const planList = document.getElementById('planList');
            planList.innerHTML = '<h3>Your Plan</h3>';
            selectedPlaces.forEach(place => {
                const p = document.createElement('p');
                p.textContent = `${place} (Added)`;
                planList.appendChild(p);
            });
            const total = document.createElement('p');
            total.className = 'total';
            total.textContent = `Total: ${selectedPlaces.length} places`;
            planList.appendChild(total);
        }

        function showPage(pageId) {
            const pages = document.querySelectorAll('.page');
            pages.forEach(page => page.classList.remove('active'));
            document.getElementById(pageId + '-page').classList.add('active');
        }

        window.onload = function() {
            updatePlanList();
        };
    </script>
</body>
</html>
<script type="module">
  // Import the functions you need from the SDKs you need
  import { initializeApp } from "https://www.gstatic.com/firebasejs/11.9.1/firebase-app.js";
  import { getAnalytics } from "https://www.gstatic.com/firebasejs/11.9.1/firebase-analytics.js";
  // TODO: Add SDKs for Firebase products that you want to use
  // https://firebase.google.com/docs/web/setup#available-libraries

  // Your web app's Firebase configuration
  // For Firebase JS SDK v7.20.0 and later, measurementId is optional
  const firebaseConfig = {
    apiKey: "AIzaSyANHYge9By7am92owY4C4moPnmBZ0CGyZ8",
    authDomain: "tripwaever.firebaseapp.com",
    projectId: "tripwaever",
    storageBucket: "tripwaever.firebasestorage.app",
    messagingSenderId: "941322506783",
    appId: "1:941322506783:web:3655a82c615cf56e53fb2a",
    measurementId: "G-B5XK9H0JX4"
  };

  // Initialize Firebase
  const app = initializeApp(firebaseConfig);
  const analytics = getAnalytics(app);
</script>
