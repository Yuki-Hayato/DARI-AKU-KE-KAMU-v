// === GALLERY IMAGE/VIDEO HANDLING ===
const galleryImage = document.getElementById("gallery-image");
const prevBtn = document.getElementById("prev-btn");
const nextBtn = document.getElementById("next-btn");
const consoleScreen = document.querySelector(".console-screen");

const mediaList = [
{ type: "image", src: "MPR1.jpg" },
{ type: "image", src: "MMR.jpg" },
{ type: "image", src: "MPR6.jpg" },
{ type: "image", src: "MPR3.jpg" },
{ type: "image", src: "MPR5.jpg" },
{ type: "image", src: "MPR4.jpg" }
];

let currentIndex = 0;
let videoElement = null;
let videoTimes = {};

function showMedia(index) {
if (index < 0) index = mediaList.length - 1;
if (index >= mediaList.length) index = 0;
currentIndex = index;

const media = mediaList[index];

// Stop and remove existing video if any
if (videoElement) {
videoTimes[videoElement.src] = videoElement.currentTime;
videoElement.pause();
videoElement.remove();
videoElement = null;
}

consoleScreen.innerHTML = "";

if (media.type === "image") {
const img = document.createElement("img");
img.src = media.src;
img.style.width = "100%";
img.style.height = "100%";
img.style.objectFit = "cover";
img.style.display = "none"; // agar tidak tampil hitam
img.onload = () => {
  img.style.display = "block";
};
consoleScreen.appendChild(img);
} else if (media.type === "video") {
const vid = document.createElement("video");
vid.src = media.src;
vid.autoplay = true;
vid.muted = false;
vid.controls = false;
vid.loop = true;
vid.volume = 1;
vid.style.width = "100%";
vid.style.height = "100%";
vid.style.objectFit = "cover";
vid.poster = "blank.png";

if (videoTimes[media.src]) {  
  vid.currentTime = videoTimes[media.src];  
}  

vid.addEventListener("timeupdate", () => {  
  videoTimes[media.src] = vid.currentTime;  
});  

consoleScreen.appendChild(vid);  
videoElement = vid;  
vid.play().catch(err => {  
  console.warn("Autoplay gagal, mungkin butuh interaksi user:", err);  
});

}
}

prevBtn.addEventListener("click", () => {
showMedia(currentIndex - 1);
});

nextBtn.addEventListener("click", () => {
showMedia(currentIndex + 1);
});

showMedia(currentIndex);

// === AUDIO PLAYER + LYRICS ===
const audio = document.getElementById("audio-player");
audio.volume = 0.5;
const playBtn = document.getElementById("play-pause-btn");
const seekBar = document.getElementById("seek-bar");
const lyricsDisplay = document.getElementById("global-lyrics-display");

const lyrics = [
{ time: 0, text: "🎵 Intro..." },
{ time: 6, text: "Hanya ingin berdiam di keheningan malam" },
{ time: 14, text: "Membayangkanmu di depanku, aura dirimu mempesonaku" },
{ time: 23, text: "Dan ku terdiam di keheningan malam" },
{ time: 32, text: "Ku ingin memastikan diri apakah ku telah jatuh hati" },
{ time: 41, text: "Kepadamu pencuri hati ♡" },
{ time: 45, text: "Yang tak ku sangka kan datang secepat ini" },
{ time: 51, text: "Padamu pencuri hati ♡" },
{ time: 55, text: "Biarkan ini menjadi melodi cinta berdua" },
{ time: 67, text: "Dan ku terdiam di keheningan malam" },
{ time: 75, text: "Ku ingin memastikan diri apakah ku telah jatuh hati ♡" },
{ time: 85, text: "Kepadamu pencuri hati ♡" },
{ time: 89, text: "Yang tak ku sangka kan datang secepat ini" },
{ time: 95, text: "Padamu pencuri hati ♡" },
{ time: 98, text: "Biarkan ini menjadi melodi cinta berdua" },
{ time: 107, text: "nyanyi sendiri🗿" }
];

audio.addEventListener("loadedmetadata", () => {
lyricsDisplay.textContent = lyrics[0].text;
seekBar.max = audio.duration;
});

audio.addEventListener("timeupdate", () => {
const currentTime = audio.currentTime;
seekBar.value = currentTime;

for (let i = lyrics.length - 1; i >= 0; i--) {
if (currentTime >= lyrics[i].time) {
if (lyricsDisplay.textContent !== lyrics[i].text) {
lyricsDisplay.textContent = lyrics[i].text;
}
break;
}
}

triggerHeartEffect(currentTime);
});

seekBar.addEventListener("input", () => {
audio.currentTime = seekBar.value;
stopHeartLoop();
cancelAllHearts();
});

playBtn.addEventListener("click", () => {
if (audio.paused) {
audio.play().then(() => playBtn.classList.add("playing"));
} else {
audio.pause();
playBtn.classList.remove("playing");
}
});

// === LOVE EFFECT ===
const canvas = document.getElementById("heart-canvas");
const ctx = canvas.getContext("2d");
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;
canvas.style.zIndex = "10";

const triggerTimes = [23, 43, 51, 67, 85, 94, 104];
let activeHearts = new Set();
let loopingHeart = false;
let heartParticles = [];
let loopAnimationId = null;

function heartPosition(t) {
return [
Math.pow(Math.sin(t), 3),
-(13 * Math.cos(t) - 5 * Math.cos(2 * t) - 2 * Math.cos(3 * t) - Math.cos(4 * t))
];
}

function scaleAndTranslate(pos, sx, sy, dx, dy) {
return [dx + pos[0] * sx, dy + pos[1] * sy];
}

function generateHeartParticles(color = "rgba(0,255,255,1)") {
const origin = [];
for (let i = 0; i < Math.PI * 2; i += 0.25)
origin.push(scaleAndTranslate(heartPosition(i), 120, 10, 0, 0));
const count = origin.length;
const target = [];
const heart = [];

for (let i = 0; i < count; i++) {
const x = Math.random() * canvas.width;
const y = Math.random() * canvas.height;
heart.push({
vx: 0,
vy: 0,
q: ~~(Math.random() * count),
D: 2 * (i % 2) - 1,
force: 0.3 * Math.random() + 0.5,
speed: Math.random() * 2 + 2,
f: color,
trace: Array.from({ length: 30 }, () => ({ x, y })),
scatter: false
});
}

return { heart, origin, target };
}

function animateHeartParticles(particles, target, origin, isLoop = false) {
let time = 0;
const count = origin.length;
const config = { traceK: 0.4, timeDelta: 0.6 };

function pulse(kx, ky) {
for (let i = 0; i < count; i++) {
target[i] = [
kx * origin[i][0] + canvas.width / 2,
ky * origin[i][1] + canvas.height / 3.2
];
}
}

function draw() {
if (!isLoop && !particles.length) return;
const n = -Math.cos(time);
pulse((1 + n) * 0.5, (1 + n) * 0.5);
time += (Math.sin(time) < 0 ? 9 : n > 0.8 ? 0.2 : 1) * config.timeDelta;
ctx.clearRect(0, 0, canvas.width, canvas.height);

for (let i = particles.length - 1; i >= 0; i--) {  
  const p = particles[i];  

  if (p.scatter) {  
    p.trace[0].x += p.vx;  
    p.trace[0].y += p.vy;  
  } else {  
    const q = target[p.q];  
    const dx = p.trace[0].x - q[0];  
    const dy = p.trace[0].y - q[1];  
    const len = Math.sqrt(dx * dx + dy * dy);  
    if (len < 10) {  
      if (Math.random() > 0.95) p.q = ~~(Math.random() * count);  
      else {  
        if (Math.random() > 0.99) p.D *= -1;  
        p.q = (p.q + p.D + count) % count;  
      }  
    }  
    p.vx += (-dx / len) * p.speed;  
    p.vy += (-dy / len) * p.speed;  
    p.trace[0].x += p.vx;  
    p.trace[0].y += p.vy;  
    p.vx *= p.force;  
    p.vy *= p.force;  
  }  

  for (let j = 0; j < p.trace.length - 1; j++) {  
    const t = p.trace[j];  
    const n = p.trace[j + 1];  
    n.x -= config.traceK * (n.x - t.x);  
    n.y -= config.traceK * (n.y - t.y);  
  }  

  ctx.fillStyle = p.f;  
  p.trace.forEach((t) => ctx.fillRect(t.x, t.y, 1.5, 1.5));  
}  

loopAnimationId = requestAnimationFrame(draw);

}

draw();
}

function createAndAnimateHeart(key) {
const { heart, origin, target } = generateHeartParticles();
animateHeartParticles(heart, target, origin);
setTimeout(() => {
heart.forEach(p => {
p.scatter = true;
p.vx = (Math.random() - 0.5) * 8;
p.vy = (Math.random() - 0.5) * 8;
});
setTimeout(() => {
ctx.clearRect(0, 0, canvas.width, canvas.height);
activeHearts.delete(key);
}, 1000);
}, 5000);
}

function triggerHeartEffect(currentTime) {
triggerTimes.forEach((sec) => {
if (Math.abs(currentTime - sec) < 0.3 && !activeHearts.has(sec)) {
activeHearts.add(sec);
createAndAnimateHeart(sec);
}
});

if (currentTime >= 104 && !loopingHeart) {
loopingHeart = true;
const { heart, origin, target } = generateHeartParticles();
heartParticles = heart;
animateHeartParticles(heartParticles, target, origin, true);
}

if (currentTime < 104 && loopingHeart) {
stopHeartLoop();
}
}

function stopHeartLoop() {
loopingHeart = false;
ctx.clearRect(0, 0, canvas.width, canvas.height);
cancelAnimationFrame(loopAnimationId);
}

function cancelAllHearts() {
activeHearts.clear();
ctx.clearRect(0, 0, canvas.width, canvas.height);
}

audio.addEventListener("ended", () => {
if (loopingHeart && heartParticles.length) {
heartParticles.forEach(p => {
p.scatter = true;
p.vx = (Math.random() - 0.5) * 8;
p.vy = (Math.random() - 0.5) * 8;
});
setTimeout(() => {
ctx.clearRect(0, 0, canvas.width, canvas.height);
loopingHeart = false;
}, 1000);
}
});



// Paksa resize layout
setTimeout(() => {
  window.dispatchEvent(new Event("resize"));
}, 200);

// Jalankan animasi flip setelah layout stabil
setTimeout(() => {
  document.querySelector('.console-container')?.classList.add('show');
}, 300);


// Fungsi untuk tunggu media siap
function waitForInitialMediaReady(callback) {
  const media = mediaList[0];

  if (media.type === "image") {
    const img = new Image();
    img.src = media.src;
    img.onload = () => callback();
  } else if (media.type === "video") {
    const vid = document.createElement("video");
    vid.src = media.src;
    vid.muted = true;
    vid.preload = "auto";
    vid.oncanplay = () => callback();
  } else {
    callback(); // fallback
  }
}

// Tampilkan animasi setelah media pertama siap
waitForInitialMediaReady(() => {
  document.querySelector('.console-container')?.classList.add('show');

  // Tambahan paksa resize biar layout stabil
  setTimeout(() => {
    window.dispatchEvent(new Event("resize"));
  }, 100);
});




document.addEventListener('touchstart', createLoveSpark, { passive: true });
document.addEventListener('mousedown', createLoveSpark);

function createLoveSpark(e) {
  const x = e.touches ? e.touches[0].clientX : e.clientX;
  const y = e.touches ? e.touches[0].clientY : e.clientY;

  for (let i = 0; i < 10; i++) {
    const spark = document.createElement('div');
    spark.className = 'spark-heart';
    document.body.appendChild(spark);

    const size = Math.random() * 8 + 8;
    spark.style.width = size + 'px';
    spark.style.height = size + 'px';
    spark.style.left = x + 'px';
    spark.style.top = y + 'px';
    spark.style.setProperty('--color', `hsl(${Math.random() * 360}, 100%, 60%)`);

    const angle = Math.random() * 2 * Math.PI;
    const radius = Math.random() * 30 + 10;
    const dx = Math.cos(angle) * radius;
    const dy = Math.sin(angle) * radius;

    spark.animate([
      { transform: `translate(0, 0) scale(1)`, opacity: 1 },
      { transform: `translate(${dx}px, ${dy}px) scale(0.3)`, opacity: 0 }
    ], {
      duration: 700,
      easing: 'ease-out'
    });

    setTimeout(() => spark.remove(), 700);
  }
}

// Nonaktifkan klik kanan di seluruh halaman
document.addEventListener('contextmenu', function(e) {
  e.preventDefault();
});

// Cegah drag/simpan gambar dan video
document.querySelectorAll('img, video').forEach(media => {
  media.setAttribute('draggable', 'false');
  media.setAttribute('oncontextmenu', 'return false');
  media.addEventListener('contextmenu', e => e.preventDefault());
});
audio.addEventListener('ended', () => {
  playBtn.classList.remove('playing');
});