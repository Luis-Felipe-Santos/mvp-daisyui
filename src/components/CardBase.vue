<script setup>
import axios from 'axios'
const emit = defineEmits(['clickEpisode'])
const props = defineProps({
  name: String,
  episode: String,
  airDate: String,
  characters: Array,
})

const charactersData = ref([])

const characterFilter = computed(() => {
  return charactersData.value.slice(0, 5)
})

const charactersCount = computed(() => {
  if (props.characters.length > 5) {
    const count = props.characters.length - 5
    return count
  }
  return 0
})

async function fetchCharacters() {
  try {
    // extrai só os IDs das URLs
    const ids = props.characters.map((url) => url.split('/').pop())
    // pega no formato [1,2,3,4,5]
    const { data } = await axios.get(`https://rickandmortyapi.com/api/character/[${ids.join(',')}]`)
    // a API retorna objeto se for 1 só, ou array se for vários
    charactersData.value = Array.isArray(data) ? data : [data]
  } catch (error) {
    console.error('Erro ao carregar personagens', error)
  }
}

onMounted(() => {
  fetchCharacters()
})
</script>

<template>
  <div
    class="card bg-base-100 shadow-sm hover:bg-neutral-content hover:shadow-md cursor-pointer"
    @click="emit('clickEpisode')"
  >
    <div class="avatar-group -space-x-6">
      <div v-for="character in characterFilter" class="avatar">
        <div class="w-12">
          <img
            :src="character.image"
            :alt="`Imagem do personagem ${character.name}`"
            :title="character.name"
          />
        </div>
      </div>
      <div v-if="charactersCount" class="avatar avatar-placeholder">
        <div class="bg-neutral text-neutral-content w-12">
          <span>+{{ charactersCount }}</span>
        </div>
      </div>
    </div>
    <div class="card-body">
      <h2 class="card-title">
        {{ name }}
        <BadgeEpisode>
          {{ episode }}
        </BadgeEpisode>
      </h2>
      <div class="card-actions justify-end">
        <BadgeAirDate>
          {{ airDate }}
        </BadgeAirDate>
      </div>
    </div>
  </div>
</template>
