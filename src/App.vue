<template>
  <a-layout style="min-height: 100vh">
    <a-layout-content
      style="display: flex; justify-content: center; align-items: center"
    >
      <router-view />
    </a-layout-content>
  </a-layout>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  setup() {},
});
</script>
