<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRouter } from 'vue-router'
import EventService from '@/services/EventService'

const router = useRouter()

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
      if(error.response && error.response.status === 404) {
        router.push({
          name: '404Resource',
          params: { resource: 'event' }
        })
      }else {
        router.push({name: 'NetworkError'})
      }
    })
})
</script>

<template>
  <div v-if="event">
    <h1>{{ event.title }}</h1>
    <div class="nav">
      <router-link :to="{ name: 'EventDetails', params: { id: id.value } }">Details</router-link> |
      <router-link :to="{ name: 'EventRegister', params: { id: id.value } }">Register</router-link>
      |
      <router-link :to="{ name: 'EventEdit', params: { id: id.value } }">Edit</router-link>
    </div>
    <router-view :event="event" />
  </div>
</template>
