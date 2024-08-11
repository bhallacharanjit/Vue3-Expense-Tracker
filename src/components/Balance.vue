<template>
  <h4>Balance</h4>
  <h2 id="balance">Rs. {{ total }}</h2>
</template>

<script setup>
import { defineProps } from "vue";
const props = defineProps({
  total: Number,
  required: true,
});
</script>
