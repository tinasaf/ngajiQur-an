<template>
     <div class="flex justify-center mt-4">
        <div class="flex space-x-4">
        <div v-for="item in items" :key="item.text" class="flex flex-col items-center">
            <i :class="item.iconClass + ' text-3xl mb-1'"></i>
            <span>{{ item.text }}</span>
        </div>
        </div>
    </div>
    <div class="flex justify-start px-4 py-2">
        <input v-model="searchQuery" type="text" placeholder="Search Surah" class="w-full lg:mx-20 mx-5 mt-9 mb-5 text-center py-3 border border-blue-500 rounded-full">
    </div>
    <div class="space-x-2 flex justify-center">
        <button v-for="item in buttons" :key="item.text" class="btn btn-ghost px-2.5 py-1 border-2 border-blue-500 rounded-full relative text-sm md:text-base text-primary-800">
            <a :href="item.link" class="text-primary-800">{{ item.text }}</a>
        </button>
    </div>
    <div class="grid lg:grid-cols-3 md:grid-cols-2 sm:grid-cols-1 gap-4 lg:mx-20 mx-5 my-5">
    <div v-for="surah in filteredQurans" :key="surah.nomor" @click="selectSurah(surah)" class="bg-secondary lg:p-2 p-1 rounded-md cursor-pointer transition duration-200 hover:scale-105 hover:shadow-lg">
      <div class="flex items-center">
        <div class="flex items-center justify-center lg:w-12 w-8 lg:h-12 h-8 lg:text-xl text-base text-base-300">{{ surah.nomor }}</div>
        <div class="flex-auto lg:w-56 w-32 ml-2">
          <div class="text-base font-bold">{{ surah.namaLatin }}</div>
          <div class="text-xs">{{ surah.arti }} - {{ surah.jumlahAyat }} Ayat</div>
        </div>
        <div class="flex-auto w-24 text-lg text-right">{{ surah.nama }}</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { defineProps, defineEmits } from 'vue';

const items = [
  { iconClass: 'bi bi-book', text: 'Al-Quran' },
  { iconClass: 'bi bi-person-lines-fill', text: 'Tahlil & Yasin' },
  { iconClass: 'bi bi-hand-index-thumb', text: 'Wirid & Doa' },
  { iconClass: 'bi bi-book-half', text: 'Maulid' }
];

const buttons = [
  { text: 'Yasin', link: '/surah/36' },
  { text: 'Al-Waqi\'ah', link: '/surah/66' },
  { text: 'Al-Mulk', link: '/surah/67' },
  { text: 'Al-Kahfi', link: '/surah/18' },
  { text: 'Ar-Rahman', link: '/surah/55' }
];

const props = defineProps({
    qurans: Array,
})

const emit = defineEmits(['selectSurah']);

const searchQuery = ref('');

const filteredQurans = computed(() => {
    return props.qurans.filter(surah => 
        surah.namaLatin.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
        surah.arti.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
        surah.nama.toLowerCase().includes(searchQuery.value.toLowerCase())
    );
});

const selectSurah = (surah) => {
    emit('selectSurah', surah);
}
</script>