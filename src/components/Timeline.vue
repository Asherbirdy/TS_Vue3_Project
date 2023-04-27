<script setup lang="ts">
import { ref, computed } from "vue";
import { today, thisWeek, thisMonth, TimelinePost } from "../posts";
import { DateTime } from "luxon";
import TimelineItem from "./TimelineItem.vue";

const periods = ["Today", "This Week", "This Month"] as const;

type Period = (typeof periods)[number];

let selectedPeriod = ref<Period>("Today");

function selectPeriod(period: Period) {
  selectedPeriod.value = period;
}

const posts = computed<TimelinePost[]>(() => {
  return [today, thisWeek, thisMonth]
    .map((post) => {
      return { ...post, created: DateTime.fromISO(post.created) };
    })
    .filter((post) => {
      if (selectedPeriod.value === "Today") {
        return post.created >= DateTime.now().minus({ day: 1 });
      }
      if (selectedPeriod.value === "This Week") {
        return post.created >= DateTime.now().minus({ week: 1 });
      }
      return post;
    });
});
</script>
<template>
  <nav class="is-primary panel">
    {{ selectedPeriod }}
    <span class="panel-tabs">
      <a
        v-for="period of periods"
        :key="period"
        :class="{ 'is-active': period === selectedPeriod }"
        @click="selectPeriod(period)"
        >{{ period }}</a
      >
    </span>
    <TimelineItem v-for="post of posts" :key="post.id" :post="post" />
  </nav>
</template>
