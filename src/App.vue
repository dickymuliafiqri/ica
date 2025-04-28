<script setup lang="ts">
import { useSound } from "@vueuse/sound";
import { onMounted, ref } from "vue";
import { invoke } from "@tauri-apps/api/core";
import backsound from "./assets/backsound.mp3";
import popup from "./assets/pop-down.mp3";

const { play, stop, isPlaying } = useSound(backsound);
const popupSound = useSound(popup);
const quoteMsg = ref("");
const loveButton = ref<HTMLButtonElement | null>(null);

async function getQuote() {
  quoteMsg.value = await invoke("get_quotes");
}

onMounted(() => {
  quoteMsg.value = "Nanti quote-nya muncul di sini...";
  addRandomEmojis();
});

function addRandomEmojis() {
  const emojiContainer = document.getElementById("emoji-container");
  const emojis = [
    "🐰",
    "🐱",
    "🐶",
    "🦄",
    "🐥",
    "🌸",
    "🍓",
    "🍒",
    "🧸",
    "🎀",
    "💖",
    "💐",
    "🌈",
    "🫧",
    "🦋",
    "🐿️",
    "🛍️",
    "🧁",
    "🍰",
    "🪄",
    "🦖",
    "🦕",
    "🙀",
    "👩🏻‍🚀",
    "🧑🏻‍🚀",
    "🪐",
    "🌏",
    "🍭",
    "🍬",
    "🍨",
    "🧁",
    "💩",
    "🎡",
  ];

  const emojiObjects: { el: HTMLElement; x: number; y: number; vx: number; vy: number }[] = [];

  for (let i = 0; i < 350; i++) {
    // Bisa diganti 350 kalau kuat performa
    const emoji = document.createElement("span");
    emoji.classList.add("emoji");
    emoji.textContent = emojis[Math.floor(Math.random() * emojis.length)];

    emoji.addEventListener("click", () => {
      popupSound.play();
      emoji.classList.add("exploding");
      setTimeout(() => emoji.remove(), 500);
    });

    emojiContainer?.appendChild(emoji);

    // Set posisi awal acak
    const x = Math.random() * window.innerWidth;
    const y = Math.random() * window.innerHeight;
    emoji.style.left = `${x}px`;
    emoji.style.top = `${y}px`;

    const speed = 1 + Math.random() * 1.5; // 1.0 - 2.5 px per frame
    const angle = Math.random() * 2 * Math.PI; // acak arah
    const vx = Math.cos(angle) * speed;
    const vy = Math.sin(angle) * speed;

    emojiObjects.push({ el: emoji, x, y, vx, vy });
  }

  function animate() {
    for (const obj of emojiObjects) {
      obj.x += obj.vx;
      obj.y += obj.vy;

      // Update dengan transform untuk lebih ringan
      obj.el.style.transform = `translate(${obj.x}px, ${obj.y}px)`;

      // Mengatur posisi emoji yang melampaui batas layar agar muncul kembali dari sisi lainnya
      if (obj.x < 0) obj.x = window.innerWidth;
      if (obj.x > window.innerWidth) obj.x = 0;
      if (obj.y < 0) obj.y = window.innerHeight;
      if (obj.y > window.innerHeight) obj.y = 0;
    }
    requestAnimationFrame(animate);
  }

  animate();
}

function moveButtonRandomly() {
  if (!loveButton.value) return;
  const top = Math.random() * 80;
  const left = Math.random() * 80;
  loveButton.value.style.position = "absolute";
  loveButton.value.style.transition = "top 0.5s, left 0.5s, transform 0.5s";
  loveButton.value.style.top = `${top}vh`;
  loveButton.value.style.left = `${left}vw`;
  loveButton.value.style.transform = `rotate(${Math.random() * 20 - 10}deg) scale(${1 + Math.random() * 0.2})`;
}
</script>
<template>
  <main class="container">
    <h1>Selamat datang di aplikasi Babycaa</h1>

    <p>Tekan tombol di bawah untuk melihat quotes harian</p>

    <form class="row" @submit.prevent="getQuote">
      <button type="submit">Lihat Quote</button>
    </form>
    <div id="quote-container">
      <p id="quote-msg">{{ quoteMsg }}</p>
    </div>

    <div id="emoji-container"></div>

    <div>
      <button @click="isPlaying ? stop() : play()">{{ isPlaying ? "Stop" : "Play" }} Music</button>
    </div>

    <div>
      <p ref="loveButton" @click="moveButtonRandomly">made with 💞</p>
    </div>
  </main>
</template>

<style>
#quote-msg {
  font-size: x-large;
  padding-left: 10%;
  padding-right: 10%;
  text-align: center;
}

#quote-container {
  margin: 1;
  margin-left: auto;
  margin-right: auto;
  max-width: max-content;
}

:root {
  font-family: Inter, Avenir, Helvetica, Arial, sans-serif;
  font-size: 16px;
  line-height: 24px;
  font-weight: 400;

  color: #0f0f0f;
  background-color: #f6f6f6;

  font-synthesis: none;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  -webkit-text-size-adjust: 100%;

  overflow: hidden;
}

.container {
  margin: 0;
  padding-top: 10vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-align: center;
  position: relative;
  z-index: 1;
}

.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: 0.75s;
}

.logo.tauri:hover {
  filter: drop-shadow(0 0 2em #24c8db);
}

.row {
  display: flex;
  justify-content: center;
}

a {
  font-weight: 500;
  color: #646cff;
  text-decoration: inherit;
}

a:hover {
  color: #535bf2;
}

h1 {
  text-align: center;
}

input,
button {
  border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.6em 1.2em;
  font-size: 1em;
  font-weight: 500;
  font-family: inherit;
  color: #0f0f0f;
  background-color: #ffffff;
  transition: border-color 0.25s;
  box-shadow: 0 2px 2px rgba(0, 0, 0, 0.2);
}

button {
  cursor: pointer;
}

button:hover {
  border-color: #396cd8;
}

button:active {
  border-color: #396cd8;
  background-color: #e8e8e8;
}

input,
button {
  outline: none;
}

@media (prefers-color-scheme: dark) {
  :root {
    color: #f6f6f6;
    background-color: #2f2f2f;
  }

  a:hover {
    color: #24c8db;
  }

  input,
  button {
    color: #ffffff;
    background-color: #0f0f0f98;
  }

  button:active {
    background-color: #0f0f0f69;
  }
}

footer {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
}

#emoji-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
}

.emoji {
  position: absolute;
  font-size: 2rem;
  opacity: 0.8;
  pointer-events: auto;
  user-select: none;
  will-change: transform, left, top;
}

@keyframes emojiMovement {
  0% {
    transform: translate(0, 0);
    opacity: 0.5;
  }
  25% {
    transform: translate(30px, 30px);
    opacity: 1;
  }
  50% {
    transform: translate(60px, 60px);
    opacity: 0.7;
  }
  75% {
    transform: translate(90px, -40px);
    opacity: 0.9;
  }
  100% {
    transform: translate(0, 0);
    opacity: 0.5;
  }
}

.pop-explode {
  animation: popExplode 0.5s forwards;
  pointer-events: none;
}

@keyframes popExplode {
  0% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(2);
    opacity: 1;
  }
  100% {
    transform: scale(0);
    opacity: 0;
  }
}

@keyframes explode {
  0% {
    transform: scale(1) rotate(0deg);
    opacity: 1;
  }
  50% {
    transform: scale(2) rotate(180deg);
    opacity: 0.7;
  }
  100% {
    transform: scale(0) rotate(360deg);
    opacity: 0;
  }
}

.exploding {
  animation: explode 0.5s ease-out forwards !important;
  pointer-events: none;
}
</style>
