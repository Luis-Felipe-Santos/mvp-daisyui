<script setup>
import axios from 'axios'

const episodes = ref([])

const page = ref(1)
const totalPages = ref(0)
const episodeSelected = ref('')
const modal = ref(false)

async function handleEpisodes() {
  try {
    const res = await axios.get('https://rickandmortyapi.com/api/episode', {
      params: {
        page: page.value,
      },
    })
    episodes.value = res.data.results
    totalPages.value = res.data.info.pages
  } catch (error) {
    console.log(error)
  }
}

function setPagination(newPage) {
  page.value = newPage
  handleEpisodes()
}

function openModal(episodeId) {
  episodeSelected.value = episodeId.toString()
  modal.value = true
}

onMounted(() => {
  handleEpisodes()
})
</script>

<template>
  <main>
    <div class="p-4">HOME</div>
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      <CardBase
        v-for="episode in episodes"
        :key="episode.id"
        :name="episode.name"
        :episode="episode.episode"
        :air-date="episode.air_date"
        :characters="episode.characters"
        @clickEpisode="openModal(episode.id)"
      />
    </div>
    <div class="flex justify-center py-4">
      <div class="join">
        <button
          v-for="(i, index) in totalPages"
          :key="index"
          class="join-item btn btn-lg md:btn-md"
          :class="{ 'btn-active': index + 1 === page }"
          @click="setPagination(index + 1)"
        >
          {{ index + 1 }}
        </button>
      </div>
    </div>
    <ModalEpisode v-model="modal" :id="episodeSelected" />
  </main>
</template>
