<script setup>
import { ref, reactive, onMounted } from 'vue';

const audio = ref(null);
const isPlaying = ref(false);
const currentTime = ref(0);
const currentTrack = ref({ title: 'Song One', artist: 'Artist One', url: '/assets/song1.mp3' });

onMounted(() => {
  audio.value.src = currentTrack.value.url;
});

const togglePlay = () => {
  if (isPlaying.value) {
    audio.value.pause();
  } else {
    audio.value.play();
  }
  isPlaying.value = !isPlaying.value;
};

const updateProgress = () => {
  currentTime.value = (audio.value.currentTime / audio.value.duration) * 100;
};

const seek = (event) => {
  const percent = event.target.value;
  audio.value.currentTime = (percent / 100) * audio.value.duration;
};

const handleFileChange = (event) => {
  const file = event.target.files[0];
  if (file && file.type.startsWith('audio')) {
    const fileURL = URL.createObjectURL(file);
    currentTrack.value = { title: file.name, artist: 'Unknown Artist', url: fileURL };
    audio.value.src = fileURL;
    audio.value.play();
    isPlaying.value = true;
  }
};

</script>

<template>
  <div class="h-screen bg-gradient-to-br from-[#bf9107] to-[#fed665] relative overflow-hidden">
    <!-- Container for Nav or Header -->
    <nav class="w-full flex items-center py-4 px-6">
      <!-- Venti Text (Logo) -->
      <h1 class="text-2xl font-bold text-[#FFD700]">Venti</h1> <!-- Gold text color -->
    </nav>
    <div class="flex flex-col items-center justify-center h-full">
      <!-- Added text above the GIF -->
      <h2 class="text-2xl font-bold text-white mb-20">Me on my way home:</h2>
      <!-- Your GIF -->
      <img
        src="https://media.tenor.com/aj2m5lTme9cAAAAM/darkville-rpg.gif"
        alt="Centered GIF"
        class="w-auto h-auto -mt-20"
      />
    </div>
    <!-- Music Player positioned at the bottom-center -->
    <div class="absolute bottom-0 left-0 w-full">
      <div class="player bg-yellow-100 text-yellow-300 p-4 rounded-md max-w-5xl mx-auto"> <!-- Max width added to maintain content's width -->
        <div class="track-info mb-4">
          <h2 class="text-2xl">{{ currentTrack.title }}</h2>
          <p class="text-sm">{{ currentTrack.artist }}</p>
        </div>
       
        <audio ref="audio" @timeupdate="updateProgress"></audio>
        <div class="controls flex justify-center items-center space-x-4 mt-4">
          <button @click="prevTrack">⏮️</button>
          <button @click="togglePlay">{{ isPlaying ? '⏸️' : '▶️' }}</button>
          <button @click="nextTrack">⏭️</button>
          <button @click="$refs.fileInput.click()">🔍 Search File</button>
        </div>
        <div class="progress-bar mt-4">
          <input type="range" v-model="currentTime" @input="seek" max="100" class="w-full">
        </div>
      </div>
      <!-- Hidden File Input -->
      <input ref="fileInput" type="file" accept="audio/*" class="hidden" @change="handleFileChange">
    </div>
  </div>
</template>
