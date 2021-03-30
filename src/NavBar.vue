<template>
  <nav class="navbar">
    <div class="navbar-end">
      <div class="buttons">
        <router-link class="button" to="/posts/new">New Post</router-link>
      </div>
    </div>
  </nav>
</template>

<script lang="ts">
  import { defineComponent } from 'vue';

  export default defineComponent({
    name: 'NavBar',
    // setup() {
    //   return {};
    // },
  });
</script>

<style scoped></style>
