<script setup>
import { ref, onMounted, computed, watchEffect } from 'vue';

import EventService from '@/services/EventService';
import EventCard from '../components/EventCard.vue';

const props = defineProps(['page']);

const events = ref(null);
const totalEvents = ref(0);

const page = computed(() => props.page);
const hasNextPage = computed(() => {
  const totalPages = Math.ceil(totalEvents.value / 2);
  return page.value < totalPages;
});

onMounted(() => {
  watchEffect(() => {
    events.value = null;
    EventService.getEvents(2, page.value)
    .then((response) => {
      events.value = response.data;
      totalEvents.value = response.headers['x-total-count'];
    })
    .catch((error) => {
      console.log(error);
    })
  });
});

</script>

<template>
  <h1>Events For Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />
    <div class="paganation">
      <router-link
        id="page-prev" 
        :to="{ name: 'event-list', query: { page: page - 1}}" 
        rel="prev"
        v-if="page != 1">
          &#60; Previous
      </router-link>
      <router-link
        id="page-next" 
        :to="{ name: 'event-list', query: { page: page + 1}}" 
        rel="next"
        v-if="hasNextPage">
        Next &#62; 
      </router-link>
    </div>
  </div>
</template>

<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.paganation {
  display: flex;
  width: 290px;
}
.paganation a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}
#page-prev {
  text-align: left;
}
#page-next {
  text-align: right;
}
</style>