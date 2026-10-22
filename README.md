
<script>
const particleBox = document.getElementById("particles");
const symbols = ["🌸", "💗", "✨", "🌷", "♡", "🌹"];
let particleTimer;

function createParticle() {
    if (particleBox.childElementCount > 45) return;

    const p = document.createElement("span");
    p.className = "particle";
    p.textContent = symbols[Math.floor(Math.random() * symbols.length)];
    p.style.left = Math.random() * 100 + "vw";
    p.style.fontSize = (12 + Math.random() * 17) + "px";

    const duration = 5 + Math.random() * 5;
    p.style.animationDuration = duration + "s";

    particleBox.appendChild(p);
    setTimeout(() => p.remove(), duration * 1000);
}

particleTimer = setInterval(createParticle, 350);

function openLetter() {
    const letter = document.getElementById("surprise");
    letter.classList.toggle("show");

    if (letter.classList.contains("show")) {
        letter.scrollIntoView({ behavior: "smooth", block: "nearest" });
        celebrate();
    }
}

function celebrate() {
    for (let i = 0; i < 24; i++) {
        setTimeout(createParticle, i * 60);
    }
}

// Giai dieu dien tu nhe, khong can file nhac ngoai
let audio = null;
let musicTimer = null;
let playing = false;
let note = 0;

const melody = [
    523.25, 659.25, 783.99, 659.25,
    587.33, 698.46, 880.00, 698.46,
    523.25, 659.25, 783.99, 1046.50
];

function playNote(freq) {
    if (!audio) return;

    const osc = audio.createOscillator();
    const gain = audio.createGain();

    osc.type = "sine";
    osc.frequency.value = freq;

    gain.gain.setValueAtTime(0.0001, audio.currentTime);
    gain.gain.exponentialRampToValueAtTime(
        0.045, audio.currentTime + 0.04
    );
    gain.gain.exponentialRampToValueAtTime(
        0.0001, audio.currentTime + 0.38
    );

    osc.connect(gain);
    gain.connect(audio.destination);
    osc.start();
    osc.stop(audio.currentTime + 0.4);
}

async function toggleMusic() {
    const btn = document.getElementById("musicBtn");

    if (playing) {
        clearInterval(musicTimer);
        playing = false;
        btn.textContent = "♫ Bật giai điệu";
        return;
    }

    try {
        const Audio = window.AudioContext || window.webkitAudioContext;
        if (!Audio) {
            alert("Trình duyệt không hỗ trợ âm thanh.");
            return;
        }

        if (!audio) audio = new Audio();
        await audio.resume();

        playing = true;
        btn.textContent = "♫ Tắt giai điệu";
        playNote(melody[note]);

        musicTimer = setInterval(() => {
            if (!playing) return;
            note = (note + 1) % melody.length;
            playNote(melody[note]);
        }, 450);
    } catch (error) {
        playing = false;
        btn.textContent = "♫ Bật giai điệu";
        alert("Không thể bật âm thanh trên trình duyệt.");
    }
}
</script>
</body>
</html>
