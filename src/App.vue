<script setup lang='ts'>
import { ref } from 'vue';
import Leafmap from './components/Leafmap.vue';

let getLocale = ref('');

console.log(getLocale);
</script>

<template>
  <Leafmap @onGetLocale='getLocale' />
  {{ getLocale }}
</template>