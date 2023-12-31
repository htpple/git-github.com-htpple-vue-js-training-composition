<script setup>
import { ref, onMounted, computed } from 'vue'
import EventService from '@/services/EventService'

const props = defineProps({
  id: {
    required: true
  }
})

const event = ref('')
const id = computed(() => props.id)
onMounted(() => {
  EventService.getEvent(id.value)
    .then((response) => {
      event.value = response.data
    })
    .catch((error) => {
      console.log(error)
    })
})
</script>

<template>
  <div v-if="event">
    <h1>{{ event.title }}</h1>
    <div class = "nav">
      <router-link :to="{ name: 'EventDetails', params: { id: id.value } }">Details</router-link> |
      <router-link :to="{ name: 'EventRegister', params: { id: id.value } }">Register</router-link>
      |
      <router-link :to="{ name: 'EventEdit', params: { id: id.value } }">Edit</router-link>
    </div>
    <router-view :event="event" />
  </div>
</template>
