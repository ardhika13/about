<!DOCTYPE html>
<html lang='en'>
<head>
    <meta charset='UTF-8'>
    <meta name='viewport' content='width=device-width, initial-scale=1.0'>
    <title>Activity Log Website</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css">
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 0;
            background: linear-gradient(135deg, #6DD5FA, #2980B9);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .header {
            background-color: #4CAF50;
            color: white;
            text-align: center;
            padding: 20px;
            font-size: 24px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            width: 100%;
            top: 0;
            left: 0;
            position: fixed;
            z-index: 999;
        }

        .header i {
            margin-right: 10px;
        }

        .container {
            width: 60%;
            background: white;
            padding: 40px;
            border-radius: 15px;
            box-shadow: 0px 6px 20px rgba(0, 0, 0, 0.1);
            transition: 0.3s ease;
            animation: fadeIn 0.6s ease-in-out;
        }

        .container:hover {
            box-shadow: 0px 8px 30px rgba(0, 0, 0, 0.2);
        }

        h2 {
            text-align: center;
            font-size: 28px;
            color: #333;
            margin-bottom: 30px;
        }

        input, button {
            width: 100%;
            padding: 14px;
            margin: 10px 0;
            border-radius: 6px;
            border: 1px solid #ccc;
            font-size: 16px;
        }

        .btn-primary {
            background-color: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
            font-weight: bold;
            transition: background-color 0.3s ease;
        }

        .btn-primary:hover {
            background-color: #45a049;
        }

        .btn {
            width: 48%;
            display: inline-block;
        }

        .table-container {
            margin-top: 30px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 18px;
            margin-top: 20px;
            transition: all 0.3s ease;
        }

        th, td {
            border: 1px solid #ddd;
            padding: 12px;
            text-align: center;
        }

        th {
            background-color: #4CAF50;
            color: white;
            font-weight: bold;
        }

        tr:hover {
            background-color: #f1f1f1;
        }

        .logout-btn, .print-btn, .clear-btn, .save-btn {
            padding: 15px;
            border: none;
            border-radius: 6px;
            width: 100%;
            margin-top: 15px;
            font-size: 18px;
            cursor: pointer;
            color: white;
            transition: background-color 0.3s ease;
        }

        .logout-btn {
            background-color: #f44336;
        }

        .logout-btn:hover {
            background-color: #e53935;
        }

        .print-btn {
            background-color: #2196F3;
        }

        .print-btn:hover {
            background-color: #1976D2;
        }

        .clear-btn {
            background-color: #FF9800;
        }

        .clear-btn:hover {
            background-color: #F57C00;
        }

        .save-btn {
            background-color: #00C853;
        }

        .save-btn:hover {
            background-color: #00B341;
        }

        .btn-container {
            display: flex;
            justify-content: space-between;
            margin-top: 30px;
            flex-wrap: wrap;
            gap: 10px;
        }

        /* Animations */
        @keyframes fadeIn {
            from {
                opacity: 0;
            }
            to {
                opacity: 1;
            }
        }

        /* Media query for mobile responsiveness */
        @media (max-width: 768px) {
            .container {
                width: 90%;
                padding: 15px;
            }
            input, button {
                font-size: 14px;
            }
            table, th, td {
                font-size: 16px;
            }
        }
    </style>
</head>
<body>

    <!-- Header with Icon -->
    <div class="header">
        <i class="fas fa-tasks"></i> Activity Log Tracker
    </div>

    <div class='container'>
        <h2>Login</h2>
        <form id='loginForm'>
            <input type='text' id='username' placeholder='Username' required />
            <input type='password' id='password' placeholder='Password' required />
            <button type='submit' class='btn-primary'><i class="fas fa-sign-in-alt"></i> Login</button>
        </form>
    </div>

    <div class='container' style='display:none;' id='mainContainer'>
        <h2>Activity Log</h2>
        <form id='activityForm'>
            <input type='text' id='no' placeholder='No' required />
            <input type='text' id='activity' placeholder='Uraian Kegiatan' required />
            <input type='text' id='time' placeholder='Waktu' required />
            <button type='submit' class='btn-primary'><i class="fas fa-plus-circle"></i> Add Activity</button>
        </form>
        <div class='table-container'>
            <table id='activityTable'>
                <thead>
                    <tr>
                        <th>No</th>
                        <th>Uraian Kegiatan</th>
                        <th>Waktu</th>
                    </tr>
                </thead>
                <tbody>
                </tbody>
            </table>
        </div>

        <!-- Button container with Save button -->
        <div class="btn-container">
            <button class='logout-btn' id='logoutBtn'><i class="fas fa-sign-out-alt"></i> Logout</button>
            <button class='print-btn' id='printBtn'><i class="fas fa-print"></i> Print Data</button>
            <button class='save-btn' id='saveBtn'><i class="fas fa-save"></i> Save Data</button>
        </div>
        <button class='clear-btn' id='clearBtn'><i class="fas fa-trash-alt"></i> Clear Data</button>
    </div>

    <script>
        // Function to sanitize input and prevent XSS
        const sanitizeInput = (input) => {
            return input.replace(/&/g, '&amp;')
                        .replace(/</g, '&lt;')
                        .replace(/>/g, '&gt;')
                        .replace(/"/g, '&quot;')
                        .replace(/'/g, '&#39;');
        };

        const loginForm = document.getElementById('loginForm');
        const mainContainer = document.getElementById('mainContainer');
        const logoutBtn = document.getElementById('logoutBtn');
        const printBtn = document.getElementById('printBtn');
        const clearBtn = document.getElementById('clearBtn');
        const saveBtn = document.getElementById('saveBtn');
        const activityForm = document.getElementById('activityForm');
        const activityTable = document.getElementById('activityTable').getElementsByTagName('tbody')[0];

        let role = '';  // Variable to store role of user (admin/user)

        loginForm.addEventListener('submit', function (event) {
            event.preventDefault();
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;

            if (username === 'admin' && password === 'admin') {
                role = 'admin';
                loginForm.parentElement.style.display = 'none';
                mainContainer.style.display = 'block';
                activityForm.style.display = 'block';  // Show form for admin
                clearBtn.style.display = 'block';      // Show clear button for admin
                saveBtn.style.display = 'block';       // Show save button for admin
            } else if (username === 'user' && password === 'user') {
                role = 'user';
                loginForm.parentElement.style.display = 'none';
                mainContainer.style.display = 'block';
                activityForm.style.display = 'none';   // Hide form for user
                clearBtn.style.display = 'none';       // Hide clear button for user
                saveBtn.style.display = 'none';        // Hide save button for user
            } else {
                alert('Invalid username or password');
            }
        });

        activityForm.addEventListener('submit', function (event) {
            event.preventDefault();
            const no = sanitizeInput(document.getElementById('no').value);
            const activity = sanitizeInput(document.getElementById('activity').value);
            const time = sanitizeInput(document.getElementById('time').value);

            const newRow = activityTable.insertRow();
            const noCell = newRow.insertCell(0);
            const activityCell = newRow.insertCell(1);
            const timeCell = newRow.insertCell(2);

            noCell.textContent = no;
            activityCell.textContent = activity;
            timeCell.textContent = time;

            document.getElementById('no').value = '';
            document.getElementById('activity').value = '';
            document.getElementById('time').value = '';
        });

        clearBtn.addEventListener('click', function () {
            activityTable.innerHTML = ''; // Clears the table
        });

        logoutBtn.addEventListener('click', function () {
            mainContainer.style.display = 'none';
            loginForm.parentElement.style.display = 'block';
            loginForm.reset();
        });

        printBtn.addEventListener('click', function () {
            window.print();
        });

        saveBtn.addEventListener('click', function () {
            alert("Data saved successfully!"); // Placeholder for save functionality
        });
    </script>
</body>
</html>
