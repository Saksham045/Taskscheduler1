# Taskscheduler1
Webpage
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-scale, initial-scale=1.0">
    <title>Daily Schedule Planner</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background: #f4f4f4;
            text-align: center;
        }
        h2 {
            background: #007bff;
            color: white;
            padding: 10px;
            margin: 0;
            font-size: 20px; me 
        }
        .container {
            max-width: 500px;
            margin: 20px auto;
            background: white;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
            position: relative;
            font-size: 14px;
        }
        label {
            font-weight: bold;
        }
        input, textarea {
            width: 100%;
            padding: 8px;
            margin: 8px 0;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-weight: bold;
        }
        .schedule {
            width: 100%;
            border-collapse: collapse;
        }
        .schedule th, .schedule td {
            border: 1px solid #ddd;
            padding: 10px;
            text-align: left;
        }
        .schedule th {
            background: #007bff;
            color: white;
        }
        .schedule input {
            width: 100%;
            border: none;
            padding: 5px;
            outline: none;
            font-weight: bold;
            background: transparent;
        }
        .warning {
            color: red;
            font-weight: bold;
            display: none;
            margin-top: 10px;
            padding: 10px;
            border: 1px solid red;
            border-radius: 5px;
        }
        .edit-btn {
            background: #ffc107;
            color: black;
            font-weight: bold;
            cursor: pointer;
            padding: 8px;
            margin-top: 10px;
            border-radius: 5px;
            display: inline-block;
        }
        button {
            margin-top: 15px;
            padding: 10px;
            border: none;
            background: #28a745;
            color: white;
            cursor: pointer;
            border-radius: 5px;
            font-weight: bold;
        }
        button:hover {
            background: #218838;
        }
        #invoiceBtn {
            margin-top: 20px;
            background-color: #007bff;
            padding: 10px 20px;
            border-radius: 5px;
            color: white;
            font-weight: bold;
            cursor: pointer;
            display: none;
        }
        #invoiceBtn:hover {
            background-color: #0056b3;
        }

        /* Invoice Styling */
        #invoiceContainer {
            margin-top: 30px;
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
            width: 80%;
            margin: 30px auto;
            text-align: left;
        }

        #invoiceContainer h3 {
            text-align: center;
            margin-bottom: 20px;
            color: #007bff;
        }

        #invoiceContainer p {
            font-size: 16px;
            margin: 5px 0;
        }

        #invoiceContainer table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }

        #invoiceContainer th, #invoiceContainer td {
            padding: 10px;
            border: 1px solid #ddd;
        }

        #invoiceContainer th {
            background-color: #007bff;
            color: white;
        }

        #invoiceContainer td {
            background-color: #f9f9f9;
        }

        .invoice-total {
            font-size: 18px;
            font-weight: bold;
            color: #28a745;
            margin-top: 20px;
        }

        .download-btn {
            background-color: #28a745;
            color: white;
            padding: 10px 20px;
            border-radius: 5px;
            font-weight: bold;
            text-align: center;
            cursor: pointer;
            display: block;
            margin-top: 20px;
        }

        .download-btn:hover {
            background-color: #218838;
        }

        /* Watermark Styling */
        .watermark {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 80px;
            color: rgba(0, 123, 255, 0.1);
            z-index: -1;
            pointer-events: none;
        }

        /* Clear Invoice button */
        #clearInvoiceBtn {
            background-color: #dc3545;
            color: white;
            padding: 10px 20px;
            border-radius: 5px;
            font-weight: bold;
            cursor: pointer;
            display: block;
            margin-top: 20px;
        }

        #clearInvoiceBtn:hover {
            background-color: #c82333;
        }

    </style>
</head>
<body>

    <h2>Daily Schedule Planner</h2>

    <div class="container" id="scheduleContainer">
        <div class="watermark">Task Scheduler</div>

        <label for="date">Select Date:</label>
        <input type="date" id="date" onchange="checkFutureDate()">

        <p id="warningMsg" class="warning">Oops 😬 You selected Future day!!</p>

        <label for="aim">Today's Aim:</label>
        <textarea id="aim" placeholder="Write your goal for today..."></textarea>

        <table class="schedule">
            <thead>
                <tr>
                    <th>Time</th>
                    <th>Task</th>
                </tr>
            </thead>
            <tbody id="scheduleBody">
                <!-- Schedule rows will be inserted dynamically -->
            </tbody>
        </table>

        <div class="edit-btn" onclick="enableEdit()">✏️ Edit Task Timings</div>

        <button onclick="saveSchedule()">Save Schedule</button>
        <div id="invoiceBtn" onclick="generateInvoice()">Generate Invoice</div>
    </div>

    <!-- Invoice Container -->
    <div id="invoiceContainer" style="display: none;">
        <h3>Invoice for Schedule</h3>
        <p><strong>Date:</strong> <span id="invoiceDate"></span></p>
        <p><strong>Aim:</strong> <span id="invoiceAim"></span></p>

        <table>
            <thead>
                <tr>
                    <th>Time</th>
                    <th>Task</th>
                </tr>
            </thead>
            <tbody id="invoiceSchedule"></tbody>
        </table>

        <p class="invoice-total">Total Tasks: <span id="totalTasks"></span></p>

        <button id="clearInvoiceBtn" onclick="clearInvoice()">Clear Invoice</button>
    </div>

    <script src="https://html2canvas.hertzen.com/dist/html2canvas.min.js"></script>

    <script>
        const scheduleTimes = [
            "06:00 - 07:00 AM", "07:00 - 09:00 AM", "09:30 - 11:30 AM", 
            "12:00 - 02:00 PM", "02:30 - 04:30 PM", "05:00 - 08:00 PM", 
            "08:00 - 10:00 PM"
        ];
        let isEditingEnabled = false;

        function setMinDate() {
            const today = new Date();
            const todayDate = today.toISOString().split('T')[0];
            
            document.getElementById("date").setAttribute("min", todayDate);
            document.getElementById("date").value = todayDate;
            loadSchedule();
        }

        function checkFutureDate() {
            const selectedDate = document.getElementById("date").value;
            const todayDate = new Date().toISOString().split('T')[0];

            document.getElementById("warningMsg").style.display = selectedDate > todayDate ? "block" : "none";
        }

        function enableEdit() {
            isEditingEnabled = true;
            document.querySelectorAll(".schedule input").forEach(input => {
                input.removeAttribute("readonly");
            });
        }

        function loadSchedule() {
            let scheduleBody = document.getElementById("scheduleBody");
            scheduleBody.innerHTML = "";

            scheduleTimes.forEach((time, index) => {
                scheduleBody.innerHTML += `
                    <tr>
                        <td><input type="text" value="${time}" id="time_${index}" readonly></td>
                        <td><input type="text" id="task_${index}" placeholder="Enter task here..."></td>
                    </tr>
                `;
            });
        }

        function saveSchedule() {
            let tasks = [];

            scheduleTimes.forEach((_, index) => {
                let timeInput = document.getElementById(`time_${index}`);
                let taskInput = document.getElementById(`task_${index}`);

                if (!isEditingEnabled) {
                    timeInput.setAttribute("readonly", true);
                }

                tasks.push({
                    time: timeInput.value,
                    task: taskInput.value
                });
            });

            localStorage.setItem("savedSchedule", JSON.stringify(tasks));
            alert("Schedule Saved!");
            document.getElementById("invoiceBtn").style.display = "block";
        }

        function generateInvoice() {
            const date = document.getElementById("date").value;
            const aim = document.getElementById("aim").value;

            document.getElementById("invoiceDate").innerText = date;
            document.getElementById("invoiceAim").innerText = aim;

            let invoiceSchedule = document.getElementById("invoiceSchedule");
            invoiceSchedule.innerHTML = "";
            let taskCount = 0;

            scheduleTimes.forEach((time, index) => {
                let taskInput = document.getElementById(`task_${index}`).value;
                if (taskInput.trim()) {
                    taskCount++;
                    invoiceSchedule.innerHTML += `
                        <tr>
                            <td>${time}</td>
                            <td>${taskInput}</td>
                        </tr>
                    `;
                }
            });

            document.getElementById("totalTasks").innerText = taskCount;
            document.getElementById("invoiceContainer").style.display = "block";
        }

        function clearInvoice() {
            localStorage.removeItem("savedSchedule");
            document.getElementById("invoiceContainer").style.display = "none";
            document.getElementById("invoiceBtn").style.display = "none";
        }

        // Initialize the page
        window.onload = setMinDate;
    </script>

</body>
</html>
<div>Pomodoro </div>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-scale, initial-scale=1.0">
    <title>Pomodoro Timer</title>
    <style>
        .pomodoro-timer {
            margin-top: 30px;
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
            text-align: center;
        }

        .timer-display {
            font-size: 30px;
            font-weight: bold;
            color: #007bff;
            margin: 10px 0;
        }

        .progress-bar {
            width: 100%;
            height: 20px;
            background-color: #ddd;
            border-radius: 10px;
            margin: 20px 0;
        }

        .progress-bar-filled {
            height: 100%;
            background-color: #28a745;
            border-radius: 10px;
            width: 0;
        }

        .pomodoro-timer button {
            margin: 5px;
            padding: 10px;
            border-radius: 5px;
            background-color: #28a745;
            color: white;
            font-weight: bold;
            cursor: pointer;
        }

        .pomodoro-timer button:hover {
            background-color: #218838;
        }

        #pomodoroStatus {
            margin-top: 15px;
            font-size: 16px;
            font-weight: bold;
            color: #28a745;
        }
    </style>
</head>
<body>

    <div class="pomodoro-timer">
        <h3>Pomodoro Timer</h3>
        <div id="pomodoroDisplay" class="timer-display">25:00</div>
        <div class="progress-bar">
            <div id="progressBarFilled" class="progress-bar-filled"></div>
        </div>
        <button id="startPomodoro" onclick="startPomodoro()">Start</button>
        <button id="pausePomodoro" onclick="pausePomodoro()" disabled>Pause</button>
        <button id="resetPomodoro" onclick="resetPomodoro()">Reset</button>
        <p id="pomodoroStatus">Focus on your task!</p>
    </div>

    <script>
        let pomodoroTimer;
        let pomodoroTime = 25 * 60; // 25 minutes
        let isPomodoroRunning = false;
        let pomodoroDisplay = document.getElementById("pomodoroDisplay");
        let startPomodoroBtn = document.getElementById("startPomodoro");
        let pausePomodoroBtn = document.getElementById("pausePomodoro");
        let resetPomodoroBtn = document.getElementById("resetPomodoro");
        let pomodoroStatus = document.getElementById("pomodoroStatus");
        let progressBarFilled = document.getElementById("progressBarFilled");

        // Function to start Pomodoro
        function startPomodoro() {
            if (isPomodoroRunning) return;
            isPomodoroRunning = true;
            startPomodoroBtn.disabled = true;
            pausePomodoroBtn.disabled = false;
            pomodoroTimer = setInterval(function() {
                if (pomodoroTime <= 0) {
                    clearInterval(pomodoroTimer);
                    pomodoroStatus.textContent = "Time for a break!";
                    startPomodoroBtn.disabled = false;
                    pausePomodoroBtn.disabled = true;
                } else {
                    pomodoroTime--;
                    let minutes = Math.floor(pomodoroTime / 60);
                    let seconds = pomodoroTime % 60;
                    pomodoroDisplay.textContent = `${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
                    updateProgressBar();
                }
            }, 1000);
        }

        // Function to pause Pomodoro
        function pausePomodoro() {
            clearInterval(pomodoroTimer);
            isPomodoroRunning = false;
            startPomodoroBtn.disabled = false;
            pausePomodoroBtn.disabled = true;
        }

        // Function to reset Pomodoro
        function resetPomodoro() {
            clearInterval(pomodoroTimer);
            isPomodoroRunning = false;
            pomodoroTime = 25 * 60; // Reset to 25 minutes
            pomodoroDisplay.textContent = "25:00";
            progressBarFilled.style.width = "0%";
            startPomodoroBtn.disabled = false;
            pausePomodoroBtn.disabled = true;
            pomodoroStatus.textContent = "Focus on your task!";
        }

        // Function to update the progress bar
        function updateProgressBar() {
            let progress = (1 - (pomodoroTime / (25 * 60))) * 100;
            progressBarFilled.style.width = `${progress}%`;
        }
    </script>

</body>
</html>
<div></div>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-scale, initial-scale=1.0">
    <title>Chat with Us Icon</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background: #f4f4f4;
        }

        /* Chat Icon Styling */
        .chat-icon {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background-color: #007bff;
            color: white;
            padding: 12px;
            border-radius: 50%;
            font-size: 20px; /* Slightly larger for readability */
            box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.3);
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .chat-icon:hover {
            background-color: #0056b3;
            transform: scale(1.1);
            box-shadow: 0px 6px 15px rgba(0, 0, 0, 0.3);
        }

        .chat-icon a {
            color: white;
            text-decoration: none;
            font-size: 24px;
        }

        .chat-icon:active {
            background-color: #004085;
            transform: scale(1);
        }

        .chat-icon:focus {
            outline: none;
        }

    </style>
</head>
<body>

    <!-- Chat Icon -->
    <div class="chat-icon">
        <a href="mailto:thecurvature8@gmail.com?subject=Query Regarding Your Service&body=Hi, I have a query regarding your service...">💬</a>
    </div>

</body>
</html>
<div>Collaboration </div>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Email Task Sender</title>
    <style>
        /* Fixed position at the bottom left of the screen */
        #composeIconContainer {
            position: fixed;
            left: 20px; /* Fixed to the left side */
            bottom: 20px;
            text-align: center;
            cursor: pointer;
        }

        /* Style for the Image (Logo) */
        #composeIcon {
            background-image: url('https://i.postimg.cc/cCvfbRGv/IMG-20250206-122104.png'); /* Replace with your logo URL */
            background-size: contain; /* Make the image fit */
            width: 80px; /* Adjusted size for better visibility */
            height: 80px; /* Adjusted size for better visibility */
            border-radius: 50%;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2); /* Shadow effect */
        }

        /* Form Style */
        #taskForm {
            display: none;
            text-align: center;
            margin-top: 20px;
        }

        label, textarea, input {
            font-size: 16px;
            margin: 10px 0;
        }

        button {
            background-color: #28a745;
            color: white;
            padding: 12px 25px;
            border-radius: 5px;
            cursor: pointer;
            border: none;
            font-size: 16px;
        }

        button:hover {
            background-color: #218838;
        }

        /* Back Button */
        #backButton {
            background-color: #ffc107;
            color: white;
            padding: 12px 25px;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 20px;
            font-size: 16px;
            border: none;
            transition: background-color 0.3s ease;
        }

        #backButton:hover {
            background-color: #e0a800; /* Darker yellow on hover */
        }
    </style>
</head>
<body>

    <!-- Circular Task Compose Button with Logo -->
    <div id="composeIconContainer" onclick="showTaskForm()">
        <div id="composeIcon"></div> <!-- Just the image (no text) -->
    </div>

    <!-- Task Compose Form -->
    <div id="taskForm">
        <h2>Compose Email</h2>

        <label for="task">Enter your task:</label><br>
        <textarea id="task" rows="4" cols="50" placeholder="Write your task here..."></textarea><br><br>

        <label for="emailTo">Enter recipient's email:</label><br>
        <input type="email" id="emailTo" placeholder="Recipient's email"><br><br>

        <button onclick="composeEmail()">Send Task via Email</button><br><br>

        <!-- Back Button -->
        <button id="backButton" onclick="goBack()">Back</button>
    </div>

    
    <script>
        // Show the email task form when icon is clicked
        function showTaskForm() {
            document.getElementById('taskForm').style.display = 'block';
            document.
