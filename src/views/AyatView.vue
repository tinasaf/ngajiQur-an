<template>
    <div class="text-2xl text-center mt-5">{{ surah.nama }}</div>
    <div class="text-xl text-center font-bold">{{ surah.namaLatin }}</div>
    <div class="flex justify-center items-center">
        <div class="text-sm">{{ surah.tempatTurun }}</div>
        <div class="text-md font-medium mx-5">{{ surah.arti }}</div>
        <div class="text-sm">{{ surah.jumlahAyat }} Ayat</div>
    </div>
    <div class="flex justify-between lg:mx-36 mx-12 my-5">
        <a href="/" class="btn  border-blue-500 rounded-full">
            <i class="bi bi-arrow-return-left lg:text-xl text-xs"></i>
            <span>Kembali</span>
        </a>
        <button @click="playAudio" class="btn  border-blue-500 rounded-full">
            <i :class="{'bi bi-play-circle': !isPlaying, 'bi bi-pause-circle': isPlaying}" class="text-xl"></i>
            <span>Putar Murottal</span>
        </button>
    </div>
    <ul class="lg:px-36 px-12">
        <li v-for="ayat in ayats" :key="ayat.nomor" class="my-10 border-b border-neutral-content">
            <div class="flex justify-between items-center">
                <div class="mr-5">{{ ayat.nomorAyat }}.</div>
                <div class="text-2xl">{{ ayat.teksArab }}</div>
            </div>
            <div class="flex items-center mt-3">
                <button @click="playAyatAudio(ayat)">
                    <i :class="{'bi bi-play-circle': !ayat.isPlaying, 'bi bi-pause-circle': ayat.isPlaying}" class="text-xl hover:opacity-70"></i>
                </button>
                <div class="ml-3 text-lg font-medium">{{ ayat.teksLatin }}</div>
            </div>
            <div class="ml-7 my-2 text-sm">{{ ayat.teksIndonesia }}</div>
        </li>
    </ul>
    <audio ref="audioPlayer" class="hidden" controls>
        Your browser does not support the audio element.
    </audio>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';
import axios from 'axios';

const route = useRoute();
const surah = ref({});
const ayats = ref([]);
const audioPlayer = ref(null);
const isPlaying = ref(false);
const currentTime = ref(0);
const currentAyat = ref(null);


onMounted(() => {
    const surahNumber = route.params.id;
    axios
        .get(`https://equran.id/api/v2/surat/${surahNumber}`)
        .then(response => {
            surah.value = response.data.data;
            ayats.value = response.data.data.ayat;
        })
        .catch(error => {
            console.error('Terjadi kesalahan saat mengambil data:', error);
        });
});

const playAudio = () => {
    if (!audioPlayer.value) return;
    if (isPlaying.value) {
        currentTime.value = audioPlayer.value.currentTime;
        audioPlayer.value.pause();
    } else {
        if (currentTime.value > 0) {
            audioPlayer.value.currentTime = currentTime.value;
        } else {
            audioPlayer.value.src = surah.value.audioFull['05'];
        }
        audioPlayer.value.play();
    }
    isPlaying.value = !isPlaying.value;
};

const playAyatAudio = (ayat) => {
    if (!audioPlayer.value) return;
    if (currentAyat.value && currentAyat.value !== ayat) {
        currentAyat.value.isPlaying = false;
        currentAyat.value.currentTime = audioPlayer.value.currentTime;
    }
    if (ayat.isPlaying) {
        ayat.currentTime = audioPlayer.value.currentTime;
        audioPlayer.value.pause();
    } else {
        audioPlayer.value.src = ayat.audio['05'];
        if (ayat.currentTime > 0) {
            audioPlayer.value.currentTime = ayat.currentTime;
        }
        audioPlayer.value.play();
    }
    ayat.isPlaying = !ayat.isPlaying;
    currentAyat.value = ayat;
};
</script>