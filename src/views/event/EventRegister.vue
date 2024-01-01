<script setup>
import { useRouter } from 'vue-router'
const router = useRouter()

const register = () => {
  router.push({
    name: 'EventDetails'
  })
}
</script>

<template>
  <p>Register for the event here!</p>
  <button @click="register">Register</button>
</template>
