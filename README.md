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

/* General Styles */
body {
    font-family: 'Arial', sans-serif;
    background-color: #f4f4f4;
    margin: 0;
    padding: 0;
    text-align: center;
}

.app-container {
    max-width: 600px;
    margin: 50px auto;
    padding: 20px;
    background: #fff;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

header {
    background: #007bff;
    color: white;
    padding: 15px;
    border-top-left-radius: 10px;
    border-top-right-radius: 10px;
    font-size: 24px;
}

.search-bar input {
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    margin-bottom: 15px;
}

#prayerTimesButton {
    padding: 10px 20px;
    background-color: #28a745;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s;
    margin-bottom: 15px;
}

#prayerTimesButton:hover {
    background-color: #218838;
}

/* Modal for prayer times */
.modal {
    display: none;
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    overflow: auto;
    background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
    background-color: white;
    margin: 10% auto;
    padding: 20px;
    border-radius: 5px;
    width: 80%;
    max-width: 400px;
    text-align: center;
}

.close {
    color: #aaa;
    float: right;
    font-size: 28px;
    font-weight: bold;
    cursor: pointer;
}

.close:hover {
    color: black;
}

.reader-selection {
    margin: 15px 0;
    text-align: right;
}

.reader-selection select {
    padding: 5px;
    border: 1px solid #ccc;
    border-radius: 5px;
}

.sura-list {
    list-style: none;
    padding: 0;
    margin: 0;
}

.sura-item {
    padding: 15px;
    margin: 5px 0;
    background: #f9f9f9;
    border: 1px solid #ddd;
    border-radius: 5px;
    cursor: pointer;
    transition: background 0.3s ease;
}

.sura-item:hover {
    background: #e0f7fa;
}

.sura-name {
    font-size: 18px;
    font-weight: bold;
    margin-bottom: 10px;
}

.buttons {
    display: flex;
    gap: 10px;
    justify-content: center;
}

.play-button, .stop-button, .download-button {
    background-color: #007bff;
    color: white;
    border: none;
    padding: 5px 10px;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s;
}

.play-button:hover {
    background-color: #0056b3;
}

.stop-button:hover {
    background-color: #dc3545;
}

.download-button:hover {
    background-color: #28a745;
}

.notification {
    margin-top: 10px;
    padding: 10px;
    background-color: #d4edda;
    color: #155724;
    border: 1px solid #c3e6cb;
    border-radius: 5px;
    display: none;
}

footer {
    margin-top: 20px;
    padding: 10px;
    background: #007bff;
    color: white;
    border-bottom-left-radius: 10px;
    border-bottom-right-radius: 10px;
    font-size: 14px;
}
// Variable to track the currently playing audio and its current time
let currentAudio = null;
let currentTime = 0;

// Fetch Quran data from AlQuran.cloud API
async function fetchQuranData(reader) {
    try {
        const response = await fetch(`https://api.alquran.cloud/v1/quran/${reader}`);
        const data = await response.json();

        if (data.code === 200 && data.data.surahs) {
            return data.data.surahs;
        } else {
            throw new Error("Failed to load Quran data.");
        }
    } catch (error) {
        console.error(error);
        alert("حدث خطأ أثناء تحميل بيانات القرآن الكريم. يرجى التحقق من اتصال الإنترنت.");
        return [];
    }
}

// Function to render the list of suras
function renderSuras(suras) {
    const suraList = document.getElementById("suraList");
    suraList.innerHTML = ""; // Clear previous content

    suras.forEach((sura, index) => {
        const suraItem = document.createElement("div");
        suraItem.className = "sura-item";

        const suraName = document.createElement("div");
        suraName.className = "sura-name";
        suraName.textContent = `${index + 1}. ${sura.name} (${sura.ayahs.length} آية)`;

        const buttonsDiv = document.createElement("div");
        buttonsDiv.className = "buttons";

        const playButton = document.createElement("button");
        playButton.className = "play-button";
        playButton.textContent = "تشغيل";

        const stopButton = document.createElement("button");
        stopButton.className = "stop-button";
        stopButton.textContent = "إيقاف";

        const downloadButton = document.createElement("button");
        downloadButton.className = "download-button";
        downloadButton.textContent = "تحميل";

        // Play all ayahs in the surah sequentially
        playButton.addEventListener("click", () => {
            const notification = document.getElementById("notification");
            notification.style.display = "block";
            notification.textContent = `جاري تشغيل سورة ${sura.name}...`;

            // Stop any currently playing audio
            if (currentAudio) {
                currentAudio.pause();
                currentAudio.currentTime = 0; // Reset the current audio
            }

            const audioSrc = sura.ayahs.map(ayah => ayah.audio);

            function playAyah(index) {
                if (index >= audioSrc.length) {
                    notification.style.display = "none";
                    return; // Stop if all ayahs are played
                }

                const audio = new Audio(audioSrc[index]);
                currentAudio = audio;

                // Set the current time if resuming playback
                audio.currentTime = currentTime;

                audio.play();

                // When the current ayah ends, play the next one
                audio.onended = () => {
                    playAyah(index + 1); // Play the next ayah
                };

                // Handle errors in case an audio file fails to load
                audio.onerror = () => {
                    alert(`فشل في تحميل صوت الآية رقم ${index + 1}`);
                    playAyah(index + 1); // Skip to the next ayah
                };
            }

            playAyah(0); // Start playing from the first ayah
        });

        // Stop the current audio and save the current time
        stopButton.addEventListener("click", () => {
            if (currentAudio) {
                currentTime = currentAudio.currentTime; // Save the current time
                currentAudio.pause();
                currentAudio = null;
                const notification = document.getElementById("notification");
                notification.style.display = "none";
                alert("تم إيقاف الصوت.");
            }
        });

        // Download the surah as an MP3 file
        downloadButton.addEventListener("click", () => {
            sura.ayahs.forEach((ayah, ayahIndex) => {
                const ayahUrl = ayah.audio;
                const ayahFilename = `${sura.name}_verse_${ayahIndex + 1}.mp3`;
                downloadFile(ayahUrl, ayahFilename);
            });
            alert(`بدأ تنزيل سورة ${sura.name} كملفات منفصلة.`);
        });

        buttonsDiv.appendChild(playButton);
        buttonsDiv.appendChild(stopButton);
        buttonsDiv.appendChild(downloadButton);

        suraItem.appendChild(suraName);
        suraItem.appendChild(buttonsDiv);

        suraList.appendChild(suraItem);
    });
}

// Update suras when reader is changed
document.getElementById("reader").addEventListener("change", async function () {
    const selectedReader = this.value;
    const suras = await fetchQuranData(selectedReader);
    renderSuras(suras);
});

// Direct search functionality
function searchSuras() {
    const query = document.getElementById("searchInput").value.toLowerCase();
    const selectedReader = document.getElementById("reader").value;

    fetchQuranData(selectedReader).then(suras => {
        const filteredSuras = suras.filter(sura =>
            sura.name.toLowerCase().includes(query)
        );
        renderSuras(filteredSuras);
    });
}

// Initialize the app by fetching and rendering all suras with default reader
const defaultReader = "ar.abdulbasitmurattal"; // Default reader: عبد الباسط عبد الصمد
fetchQuranData(defaultReader).then(suras => {
    renderSuras(suras);
});

// Show prayer times modal
document.getElementById("prayerTimesButton").addEventListener("click", function () {
    const modal = document.getElementById("prayerTimesModal");
    modal.style.display = "block";
});

// Close prayer times modal
document.getElementsByClassName("close")[0].addEventListener("click", function () {
    const modal = document.getElementById("prayerTimesModal");
    modal.style.display = "none";
});

// Function to download a single file
function downloadFile(url, filename) {
    const link = document.createElement("a");
    link.href = url;
    link.download = filename;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
}
