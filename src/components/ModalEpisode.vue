<script setup>
import axios from 'axios'

const props = defineProps({
  id: String,
})

const episode = ref(null)
const charactersData = ref([])
const showModal = defineModel()

async function handleEpisodes() {
  try {
    const res = await axios.get(`https://rickandmortyapi.com/api/episode/${props.id}`)
    episode.value = res.data

    await fetchCharacters(res.data.characters)
  } catch (error) {
    console.log(error)
  }
}

async function fetchCharacters(characters) {
  try {
    const ids = characters.map((url) => url.split('/').pop())
    const { data } = await axios.get(`https://rickandmortyapi.com/api/character/[${ids.join(',')}]`)
    charactersData.value = Array.isArray(data) ? data : [data]
  } catch (error) {
    console.error('Erro ao carregar personagens', error)
  }
}

watch(showModal, (newValue) => {
  if (newValue) {
    handleEpisodes()
  } else {
    episode.value = null
    charactersData.value = []
  }
})

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
      <div v-for="character in charactersData" class="avatar">
        <div class="w-16 rounded-full">
          <img
            :src="character.image"
            :alt="`Imagem do personagem ${character.name}`"
            :title="character.name"
          />
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
