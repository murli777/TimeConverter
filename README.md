<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Time Zone Converter</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f4f8;
            color: #333;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }

        .container {
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
            text-align: center;
            max-width: 600px;
            width: 100%;
        }

        h1 {
            font-size: 24px;
            color: #333;
        }

        .form-group {
            margin: 15px 0;
        }

        input, select, button {
            padding: 8px;
            margin-top: 10px;
            border-radius: 4px;
            border: 1px solid #ccc;
        }

        button {
            background-color: #007bff;
            color: white;
            border: none;
            cursor: pointer;
        }

        button:hover {
            background-color: #0056b3;
        }

        #result {
            margin-top: 20px;
        }

        #converted-time {
            font-size: 20px;
            font-weight: bold;
        }

        .dropdown-container {
            position: relative;
            display: inline-block;
            width: 100%;
        }

        .dropdown-toggle {
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: pointer;
            background: #f0f4f8;
            padding: 8px;
            border-radius: 4px;
            border: 1px solid #ccc;
        }

        .dropdown-arrow {
            border: solid #333;
            border-width: 0 2px 2px 0;
            display: inline-block;
            padding: 3px;
            margin-left: 10px;
            transform: rotate(45deg);
            transition: transform 0.2s;
        }

        .dropdown-menu {
            position: absolute;
            top: 100%;
            left: 0;
            right: 0;
            max-height: 300px;
            overflow-y: auto;
            border: 1px solid #ccc;
            background: white;
            z-index: 1000;
            display: none;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }

        .dropdown-menu div {
            padding: 8px;
            cursor: pointer;
        }

        .dropdown-menu div:hover {
            background-color: #f0f0f0;
        }

        .dropdown-menu.show {
            display: block;
        }

        .searchable-dropdown input {
            width: calc(100% - 20px);
            box-sizing: border-box;
            padding: 8px;
            border: 1px solid #ccc;
            border-radius: 4px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Time Zone Converter</h1>
        <p>Convert between different time zones with date adjustment.</p>
        
        <div class="form-group">
            <label for="input-date">Enter Date:</label>
            <input type="date" id="input-date">
        </div>

        <div class="form-group">
            <label for="input-time">Enter Time (12-hour format):</label>
            <input type="number" id="input-hours" placeholder="HH" min="1" max="12" style="width: 50px;">
            <input type="number" id="input-minutes" placeholder="MM" min="0" max="59" style="width: 50px;">
            <select id="am-pm">
                <option value="AM">AM</option>
                <option value="PM">PM</option>
            </select>
        </div>
        
        <div class="form-group dropdown-container">
            <label for="from-timezone">From Time Zone:</label>
            <div id="from-timezone-toggle" class="dropdown-toggle">
                Select Time Zone
                <div class="dropdown-arrow"></div>
            </div>
            <div class="searchable-dropdown">
                <input type="text" id="from-timezone" placeholder="Search or type timezone">
                <div class="dropdown-menu" id="from-timezone-menu"></div>
            </div>
        </div>

        <div class="form-group dropdown-container">
            <label for="to-timezone">To Time Zone:</label>
            <div id="to-timezone-toggle" class="dropdown-toggle">
                Select Time Zone
                <div class="dropdown-arrow"></div>
            </div>
            <div class="searchable-dropdown">
                <input type="text" id="to-timezone" placeholder="Search or type timezone">
                <div class="dropdown-menu" id="to-timezone-menu"></div>
            </div>
        </div>

        <button onclick="convertTime()">Convert Time</button>

        <div id="result">
            <h2>Converted Date and Time:</h2>
            <p id="converted-time">--:-- AM/PM on DD/MM/YYYY</p>
        </div>
    </div>

    <script>
        const timeZones = [
            'India-IST', 'Philippines-PHT', 'America/New_York-EST', 'America/Los_Angeles-PST', 'America/Chicago-CST',
            'America/Denver-MST', 'America/Anchorage-AKST', 'America/Halifax-ADT', 'America/Sao_Paulo-BRT',
            'America/Buenos_Aires-ART', 'Europe/London-GMT', 'Europe/Paris-CET', 'Europe/Berlin-CET', 'Europe/Moscow-MS',
            'Europe/Istanbul-TRT', 'Asia/Tokyo-JST', 'Asia/Shanghai-CST', 'Asia/Singapore-SGT', 'Asia/Hong_Kong-HKT',
            'Asia/Seoul-KST', 'Asia/Kolkata-IST', 'Asia/Dubai-GST', 'Australia/Sydney-AEDT', 'Australia/Melbourne-AEDT',
            'Australia/Brisbane-AEST', 'Australia/Adelaide-ACDT', 'Australia/Perth-AWST', 'Pacific/Auckland-NZDT',
            'Pacific/Fiji-FJT', 'Pacific/Honolulu-HST', 'Pacific/Chatham-CHAST', 'Pacific/Guam-GMT+10', 'Pacific/Samoa-SST',
            'Pacific/Tahiti-TAH', 'Asia/Kathmandu-NPT', 'Asia/Dhaka-BDT', 'Asia/Colombo-IST', 'Asia/Yangon-MM',
            'Asia/Vientiane-ICT', 'Asia/Manila-PHT', 'Asia/Jakarta-WIT', 'Asia/Taipei-CST', 'Asia/Ho_Chi_Minh-ICT',
            'Asia/Ulaanbaatar-ULAT', 'Asia/Pyongyang-KST', 'Asia/Thimphu-BTT', 'Asia/Almaty-Almaty', 'Asia/Novosibirsk-NSK',
            'Asia/Krasnoyarsk-KRA', 'Asia/Omsk-OMS', 'Asia/Yekaterinburg-SVT', 'Asia/Kamchatka-KAM', 'Asia/Karachi-PKST',
            'Asia/Baghdad-AST', 'Asia/Beirut-EET', 'Asia/Jerusalem-IST', 'Asia/Amman-IST', 'Asia/Damascus-EET',
            'Asia/Qatar-AST', 'Asia/Bahrain-AST', 'Asia/Kuwait-AST', 'Asia/Calcutta-IST', 'Asia/Chennai-IST',
            'Asia/Hyderabad-IST', 'Asia/Islamabad-PKST', 'Asia/Lahore-PKST', 'Asia/Muscat-GST', 'Asia/Tbilisi-TBIST',
            'Asia/Yerevan-AMT', 'Asia/Port_Moresby-PMST', 'Australia/Currie-AEST', 'Australia/Darwin-ACT', 'Australia/Lord_Howe-AEDT',
            'Australia/Canberra-AEDT', 'Australia/ACT-ACT', 'Australia/South-ACT', 'Australia/North-ACT', 'Australia/Tasmania-AEDT',
            'Pacific/Tarawa-KIT', 'Pacific/Wallis-WFT', 'Pacific/Apia-WST', 'Pacific/Chuuk-CHUT', 'Pacific/Ponape-PONT',
            'Pacific/Efate-VUT', 'Pacific/Kiritimati-LINT', 'Asia/Tehran-IRST', 'Asia/Dubai-GST', 'Asia/Ashgabat-TMT',
            'Asia/Samarkand-UZT', 'Asia/Tashkent-UZT', 'Asia/Kabul-AFT', 'Asia/Tokyo-JST', 'Asia/Saigon-ICT',
            'Asia/Colombo-IST', 'Asia/Amman-IST'
        ];

        function populateDropdown() {
            const fromMenu = document.getElementById('from-timezone-menu');
            const toMenu = document.getElementById('to-timezone-menu');

            timeZones.forEach(zone => {
                const divFrom = document.createElement('div');
                divFrom.textContent = zone;
                divFrom.dataset.value = zone;
                divFrom.onclick = () => {
                    document.getElementById('from-timezone').value = zone;
                    document.getElementById('from-timezone-toggle').textContent = zone;
                    fromMenu.style.display = 'none';
                };
                fromMenu.appendChild(divFrom);

                const divTo = document.createElement('div');
                divTo.textContent = zone;
                divTo.dataset.value = zone;
                divTo.onclick = () => {
                    document.getElementById('to-timezone').value = zone;
                    document.getElementById('to-timezone-toggle').textContent = zone;
                    toMenu.style.display = 'none';
                };
                toMenu.appendChild(divTo);
            });
        }

        function filterDropdown(inputId, menuId) {
            const input = document.getElementById(inputId);
            const menu = document.getElementById(menuId);
            const filter = input.value.toLowerCase();
            const items = menu.getElementsByTagName('div');
            Array.from(items).forEach(item => {
                const text = item.textContent.toLowerCase();
                item.style.display = text.indexOf(filter) > -1 ? 'block' : 'none';
            });
        }

        document.getElementById('from-timezone').addEventListener('input', () => filterDropdown('from-timezone', 'from-timezone-menu'));
        document.getElementById('to-timezone').addEventListener('input', () => filterDropdown('to-timezone', 'to-timezone-menu'));

        document.getElementById('from-timezone-toggle').addEventListener('click', () => {
            const menu = document.getElementById('from-timezone-menu');
            menu.classList.toggle('show');
        });

        document.getElementById('to-timezone-toggle').addEventListener('click', () => {
            const menu = document.getElementById('to-timezone-menu');
            menu.classList.toggle('show');
        });

        function convertTime() {
            const date = document.getElementById('input-date').value;
            const hours = parseInt(document.getElementById('input-hours').value);
            const minutes = parseInt(document.getElementById('input-minutes').value);
            const ampm = document.getElementById('am-pm').value;
            const fromZone = document.getElementById('from-timezone').value;
            const toZone = document.getElementById('to-timezone').value;

            if (!date || isNaN(hours) || isNaN(minutes) || !fromZone || !toZone) {
                document.getElementById('converted-time').textContent = 'Please fill all fields.';
                return;
            }

            const fromZoneOffset = moment.tz(fromZone).utcOffset();
            const toZoneOffset = moment.tz(toZone).utcOffset();

            const inputDateTime = moment(`${date} ${hours}:${minutes} ${ampm}`, 'YYYY-MM-DD hh:mm A');
            const utcDateTime = inputDateTime.utc();
            const convertedDateTime = utcDateTime.clone().add(toZoneOffset - fromZoneOffset, 'minutes');

            document.getElementById('converted-time').textContent = convertedDateTime.format('dddd, DD/MM/YYYY, hh:mm A');
        }

        populateDropdown();
    </script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.29.4/moment.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/moment-timezone/0.5.41/moment-timezone-with-data.min.js"></script>
</body>
</html>
