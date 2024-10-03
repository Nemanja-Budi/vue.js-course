<script setup>
import { ref, onMounted } from 'vue';

import EventService from '@/services/EventService';

const event = ref(null);
const props = defineProps({
    id: {
        required: true
    }
});

onMounted(() => {
    EventService.getEvent(props.id)
    .then((response) => {
        event.value = response.data;
    })
    .catch((error) => {
        console.log(error);
    });
});
</script>

<template>
    <div v-if="event">
        <h1>{{ event.title }}</h1>
        <div id="nav">
            <router-link :to="{ name: 'event-details', params: { id }}">Details</router-link> |
            <router-link :to="{ name: 'event-register', params: { id }}">Register</router-link> |
            <router-link :to="{ name: 'event-edit', params: { id }}">Edit</router-link>
        </div>
        <router-view :event="event" />
    </div>
</template>

<style scoped>
#nav {
  padding: 30px;
}
#nav a {
  font-weight: bold;
  color: #2c3e50;
}
#nav a.router-link-exact-active {
  color: #42b983;
}
</style>