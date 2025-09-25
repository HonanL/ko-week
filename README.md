<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weekly Schedule Tracker</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
        }
        input[type="number"]::-webkit-inner-spin-button, 
        input[type="number"]::-webkit-outer-spin-button { 
            -webkit-appearance: none; 
            margin: 0; 
        }
    </style>
</head>
<body class="bg-gray-100 flex items-center justify-center min-h-screen p-4">

    <!-- Main Container -->
    <div class="bg-white p-8 rounded-2xl shadow-xl w-full max-w-xl">
        <h1 class="text-3xl sm:text-4xl font-extrabold text-center text-gray-800 mb-2">Weekly Goal Tracker</h1>
        <p class="text-center text-gray-500 mb-8">Set your weekly goal and track your hours each day.</p>

        <!-- Target Hours Section -->
        <div class="mb-6">
            <label for="targetHours" class="block text-sm font-medium text-gray-700 mb-2">Weekly Goal (Total Hours)</label>
            <input type="number" id="targetHours" value="40" placeholder="e.g., 40" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-sky-500 focus:border-sky-500 transition-all duration-200">
        </div>

        <hr class="my-6 border-gray-200">

        <!-- Daily Hours Section -->
        <h2 class="text-xl font-bold text-gray-800 mb-4">Daily Hours Worked</h2>
        <div class="space-y-4 mb-6">
            <!-- Day inputs will be dynamically generated here -->
        </div>

        <!-- Results Section -->
        <div class="bg-sky-50 p-6 rounded-xl border border-sky-200 mb-6">
            <div class="flex justify-between items-center mb-4">
                <span class="text-sm font-medium text-gray-600">Total Hours Worked:</span>
                <span id="totalHoursWorked" class="text-lg font-bold text-gray-800">0</span>
            </div>
            <div class="flex justify-between items-center mb-4">
                <span class="text-sm font-medium text-gray-600">Remaining Hours:</span>
                <span id="remainingHours" class="text-2xl font-extrabold text-sky-600">0</span>
            </div>
            <p class="text-sm text-gray-500 mt-2">To reach your goal, you need to work an average of:</p>
            <p class="text-lg font-bold text-gray-800 mt-2">
                <span id="dailyAverageDecimal">0.0</span> hours/day
            </p>
            <p class="text-sm text-gray-500 mt-2">or about</p>
            <p class="text-lg font-bold text-gray-800">
                <span id="dailyAverageHours">0</span> hours and <span id="dailyAverageMinutes">0</span> minutes per day.
            </p>
        </div>

        <!-- Reset Button -->
        <div class="flex justify-center">
            <button id="resetBtn" class="bg-gray-200 text-gray-800 px-6 py-3 rounded-lg font-semibold hover:bg-gray-300 transition-colors duration-200 shadow-sm">
                Reset
            </button>
        </div>

    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
            const targetHoursInput = document.getElementById('targetHours');
            const dailyHoursContainer = document.querySelector('.space-y-4');
            const totalHoursWorkedEl = document.getElementById('totalHoursWorked');
            const remainingHoursEl = document.getElementById('remainingHours');
            const dailyAverageDecimalEl = document.getElementById('dailyAverageDecimal');
            const dailyAverageHoursEl = document.getElementById('dailyAverageHours');
            const dailyAverageMinutesEl = document.getElementById('dailyAverageMinutes');
            const resetBtn = document.getElementById('resetBtn');

            // Function to dynamically generate daily input fields
            const generateDailyInputs = () => {
                dailyHoursContainer.innerHTML = ''; // Clear existing inputs
                days.forEach(day => {
                    const div = document.createElement('div');
                    div.className = 'flex items-center justify-between';
                    div.innerHTML = `
                        <label for="${day}" class="text-sm font-medium text-gray-700 w-24">${day}</label>
                        <input type="number" id="${day}" min="0" value="0" placeholder="0" class="flex-1 ml-4 px-4 py-2 border border-gray-300 rounded-lg text-right focus:ring-2 focus:ring-sky-500 focus:border-sky-500 transition-all duration-200">
                    `;
                    dailyHoursContainer.appendChild(div);
                });
            };

            // Function to perform the calculation
            const updateCalculations = () => {
                const targetHours = parseFloat(targetHoursInput.value) || 0;
                let totalHoursWorked = 0;
                let daysLeft = 0;

                const todayIndex = new Date().getDay(); // 0 = Sunday, 1 = Monday, etc.

                days.forEach((day, index) => {
                    const input = document.getElementById(day);
                    const hours = parseFloat(input.value) || 0;
                    
                    if (index < todayIndex) {
                        totalHoursWorked += hours;
                    } else if (index > todayIndex) {
                        daysLeft++;
                    }
                });
                
                // Add today's hours to total and consider it a day worked
                const todayHoursInput = document.getElementById(days[todayIndex]);
                totalHoursWorked += parseFloat(todayHoursInput.value) || 0;
                daysLeft = days.length - (todayIndex + 1);

                const remainingHours = Math.max(0, targetHours - totalHoursWorked);
                
                totalHoursWorkedEl.textContent = totalHoursWorked.toFixed(1);
                remainingHoursEl.textContent = remainingHours.toFixed(1);
                
                if (daysLeft > 0) {
                    const dailyAverage = remainingHours / daysLeft;
                    const dailyHours = Math.floor(dailyAverage);
                    const dailyMinutes = Math.round((dailyAverage - dailyHours) * 60);

                    dailyAverageDecimalEl.textContent = dailyAverage.toFixed(1);
                    dailyAverageHoursEl.textContent = dailyHours;
                    dailyAverageMinutesEl.textContent = dailyMinutes;
                } else {
                    dailyAverageDecimalEl.textContent = "N/A";
                    dailyAverageHoursEl.textContent = "0";
                    dailyAverageMinutesEl.textContent = "0";
                }
            };

            // Function to reset all inputs
            const resetInputs = () => {
                targetHoursInput.value = 40;
                days.forEach(day => {
                    const input = document.getElementById(day);
                    input.value = 0;
                });
                updateCalculations();
            };

            // Initial setup
            generateDailyInputs();
            updateCalculations();

            // Add event listeners for changes
            targetHoursInput.addEventListener('input', updateCalculations);
            dailyHoursContainer.addEventListener('input', updateCalculations);
            resetBtn.addEventListener('click', resetInputs);
        });
    </script>
</body>
</html>
