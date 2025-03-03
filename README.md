# Holy-Quran-application-
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quran Sounds</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="app-container">
        <header>
            <h1>Quran Sounds</h1>
        </header>

        <div class="search-bar">
            <input type="text" id="searchInput" placeholder="ابحث عن السورة..." oninput="searchSuras()">
        </div>

        <button id="prayerTimesButton">أوقات الصلاة</button>

        <div class="reader-selection">
            <label for="reader">اختر القارئ:</label>
            <select id="reader">
                <option value="ar.abdulbasitmurattal">عبد الباسط عبد الصمد</option>
                <option value="ar.alafasy">محمد العفاسي</option>
                <option value="ar.husary">محمود خليل الحصري</option>
                <option value="ar.minshawi">محمد صديق المنشاوي</option>
            </select>
        </div>

        <div id="suraList" class="sura-list"></div>

        <div id="notification" class="notification"></div>

        <!-- Modal for Prayer Times -->
        <div id="prayerTimesModal" class="modal">
            <div class="modal-content">
                <span class="close">&times;</span>
                <h2>أوقات الصلاة</h2>
                <iframe 
                    id="iframe1" 
                    style="background: rgb(255, 255, 255); border: none; width: 100%; height: 200px;" 
                    src="https://timesprayer.today/widget_frame.php?frame=1&amp;id=8198&amp;sound=false&amp;theme=w3-blue&amp;lang=ar">
                </iframe>
            </div>
        </div>

        <footer>
            Quran Sounds - إصدار 1.0
        </footer>
    </div>

    <script src="script.js"></script>
</body>
</html>
