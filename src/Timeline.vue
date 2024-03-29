<template>
  <nav class="is-primary panel">
    <p class="panel-tabs">
      <a
        v-for="period in periods"
        :key="period"
        data-test="period"
        :class="[period === selectedPeriod ? 'is-active' : '']"
        @click="setPeriod(period)"
        >{{ period }}</a
      >
    </p>
    <TimelinePost v-for="post in posts" :key="post.id" :post="post" />
  </nav>
</template>

<script lang="ts">
  import { defineComponent, ref, computed } from 'vue';
  import moment from 'moment';
  import TimelinePost from './TimelinePost.vue';

  import { Period, Post } from './types';
  import { useStore } from './store';

  export default defineComponent({
    components: {
      TimelinePost,
    },

    // Blocks rendering of template for 2 seconds to imitate a page load
    async setup() {
      const periods: Period[] = ['today', 'this week', 'this month'];
      const selectedPeriod = ref<Period>('today');

      const store = useStore();
      if (!store.getState().posts.loaded) {
        await store.fetchPosts();
      }

      const allPosts = store.getState().posts.ids.reduce<Post[]>((acc, id) => {
        const post = store.getState().posts.all[id];
        return acc.concat(post);
      }, []);

      const posts = computed(() =>
        allPosts.filter((post) => {
          if (selectedPeriod.value === 'today' && post.created.isAfter(moment().subtract(1, 'day'))) {
            return true;
          }
          if (selectedPeriod.value === 'this week' && post.created.isAfter(moment().subtract(7, 'days'))) {
            return true;
          }
          if (selectedPeriod.value === 'this month' && post.created.isAfter(moment().subtract(1, 'month'))) {
            return true;
          }

          return false;
        })
      );

      const setPeriod = (period: Period) => {
        ref<Period>(period);
        selectedPeriod.value = period;
      };

      return {
        periods,
        selectedPeriod,
        setPeriod,
        posts,
      };
    },
  });
</script>
