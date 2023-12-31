<script setup>
import { ref, onMounted, computed, watchEffect } from 'vue'
import EventService from '@/services/EventService.js'
import EventCard from '@/components/EventCard.vue'

const props = defineProps(['page'])

const events = ref(null)
const totalEvents = ref(0)
const page = computed(() => props.page)

const totalPages = computed(() => {
  return Math.ceil(totalEvents.value)
})

const hasNextPage = computed(() => {
  return page.value < totalPages.value
})

const paginationRange = computed(() => {
  const sidePages = 1
  const range = []
  const start = Math.max(1, page.value - sidePages)
  const end = Math.min(page.value + sidePages, totalPages.value)

  for (let i = start; i <= end; i++) {
    range.push(i)
  }

  if (start > 1) {
    range.unshift(1)
  }
  if (end < totalPages.value) {
    range.push(totalPages.value)
  }

  return range
})

onMounted(() => {
  watchEffect(() => {
    events.value = null
    EventService.getEvents(1, page.value)
      .then((response) => {
        events.value = response.data
        totalEvents.value = response.headers['x-total-count']
      })
      .catch((error) => {
        console.log(error)
      })
  })
})
</script>

<template>
  <h1>Events for good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />

    <div class="pagination">
      <router-link
        id="page-prev"
        :to="{ name: 'event-list', query: { page: page - 1 } }"
        rel="prev"
        v-if="page != 1"
        >&#60; Previous</router-link
      >

      <div class="pagination-pages" :class="{ center: !hasNextPage || page == 1 }">
        <router-link
          v-for="pageNum in paginationRange"
          :key="pageNum"
          :to="{ name: 'event-list', query: { page: pageNum } }"
          :class="{ active: pageNum === page }"
          >{{ pageNum }}</router-link
        >
      </div>

      <router-link
        id="page-next"
        :to="{ name: 'event-list', query: { page: page + 1 } }"
        rel="next"
        v-if="hasNextPage"
        >Next &#62;</router-link
      >
    </div>
  </div>
</template>

<style scope>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.pagination {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 290px;
}
.pagination-pages {
  display: flex;
  column-gap: 10px;
}
.pagination-pages.center {
  flex: 1;
  justify-content: center;
}
.pagination a {
  text-decoration: none;
  color: #2c3e50;
}
.pagination a.active {
  font-weight: bold;
  text-decoration: underline;
}
#page-prev {
  text-align: left;
}
#page-next {
  text-align: right;
}
</style>
