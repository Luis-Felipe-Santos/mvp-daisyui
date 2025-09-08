<script setup>
import axios from 'axios'

const props = defineProps({
  id: String,
})

const episode = ref(null)
const showModal = defineModel()

async function handleEpisodes() {
  try {
    const res = await axios.get(`https://rickandmortyapi.com/api/episode/${props.id}`)
    episode.value = res.data
  } catch (error) {
    console.log(error)
  }
}

watch(showModal, (newValue) => {
  if (newValue) {
    handleEpisodes()
  } else {
    episode.value = null
  }
})

function getImage(url) {
  const newUrl = url.replace(
    'https://rickandmortyapi.com/api/character/',
    'https://rickandmortyapi.com/api/character/avatar/',
  )
  return `${newUrl}.jpeg`
}

function closeModal() {
  showModal.value = false
}
</script>

<template>
  <dialog id="modal-episode" class="modal" :class="{ 'modal-open': showModal }">
    <div v-if="episode" class="modal-box">
      <form method="dialog">
        <button
          @click="closeModal()"
          class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2"
        >
          ✕
        </button>
      </form>
      <h3 class="text-lg font-bold mb-4">
        {{ episode.name }}<BadgeEpisode>{{ episode.episode }}</BadgeEpisode>
      </h3>
      <div v-for="character in episode.characters" class="avatar">
        <div class="w-16 rounded-full">
          <img :src="getImage(character)" />
        </div>
      </div>
      <p class="py-4">
        <BadgeAirDate>{{ episode.air_date }}</BadgeAirDate>
      </p>
    </div>
    <div v-else class="modal-box">
      <div class="flex justify-center">
        <span class="loading loading-ring loading-xl"></span>
      </div>
    </div>
  </dialog>
</template>
