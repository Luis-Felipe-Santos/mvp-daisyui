<script setup>
const emit = defineEmits(['clickEpisode'])
const props = defineProps({
  name: String,
  episode: String,
  airDate: String,
  characters: Array,
})

const characterFilter = computed(() => {
  if (props.characters.length > 5) {
    return props.characters.splice(0, 5)
  }
  return props.characters
})

const charactersCount = computed(() => {
  if (props.characters.length > 5) {
    const count = props.characters.length - 5
    return count
  }
  return 0
})

function getImage(url) {
  const newUrl = url.replace(
    'https://rickandmortyapi.com/api/character/',
    'https://rickandmortyapi.com/api/character/avatar/',
  )
  return `${newUrl}.jpeg`
}
</script>

<template>
  <div
    class="card bg-base-100 shadow-sm hover:bg-neutral-content hover:shadow-md cursor-pointer"
    @click="emit('clickEpisode')"
  >
    <div class="avatar-group -space-x-6">
      <div v-for="character in characterFilter" class="avatar">
        <div class="w-12">
          <img :src="getImage(character)" />
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
