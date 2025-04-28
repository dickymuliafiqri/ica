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

const emojis = [
  "🎈",
  "🎉",
  "✨",
  "🌟",
  "🎶",
  "🍀",
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

function createEmoji() {
  const emojiContainer = document.getElementById("emoji-container");
  if (!emojiContainer) return;

  const emoji = document.createElement("div");
  emoji.textContent = emojis[Math.floor(Math.random() * emojis.length)];
  emoji.style.position = "absolute";
  emoji.style.fontSize = `${Math.random() * 24 + 24}px`;
  emoji.style.left = `${Math.random() * 100}vw`;
  emoji.style.top = `${Math.random() * 100}vh`;
  emoji.style.cursor = "pointer";
  emoji.style.transition = "transform 0.5s ease-out, opacity 0.5s ease-out";
  emoji.style.userSelect = "none";
  emoji.style.zIndex = "0";

  const dx = (Math.random() - 0.5) * 0.5;
  const dy = (Math.random() - 0.5) * 0.5;

  let x = parseFloat(emoji.style.left);
  let y = parseFloat(emoji.style.top);

  const moveInterval = setInterval(() => {
    x += dx;
    y += dy;

    if (x < 0) x = 100;
    if (x > 100) x = 0;
    if (y < 0) y = 100;
    if (y > 100) y = 0;

    emoji.style.left = `${x}vw`;
    emoji.style.top = `${y}vh`;
  }, 50);

  emoji.addEventListener("click", () => {
    emoji.style.transform = "scale(2)";
    emoji.style.opacity = "0";
    setTimeout(() => {
      clearInterval(moveInterval);
      emoji.remove();
    }, 500);
    popupSound.play();
  });

  emojiContainer.appendChild(emoji);
}

function addRandomEmojis() {
  setInterval(createEmoji, 1000);
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
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  pointer-events: auto;
  z-index: -1;
}
</style>
