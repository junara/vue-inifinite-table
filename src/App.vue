<template>
  <div ref="itemListRef" style="overflow-y: scroll; height: 200px; border: solid">
    <div v-for="item in items">
      {{ item.id }}
    </div>
  </div>
  <div>{{ `items.length = ${items.length}` }}</div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';
import { useInfiniteScroll } from '@vueuse/core';
interface Item {
  id: number;
}
const initialId = ref(0);
const initialItems = Array.from(Array(20).keys()).map((i) => ({ id: initialId.value + i }));
const items = ref<Item[]>(initialItems);

const itemListRef = ref<HTMLElement | null>(null);

onMounted(() => {
  itemListRef.value?.scrollTo(0, itemListRef.value.scrollTop + distanceBottom + 1);
});

const distanceTop = 10;
const { isLoading: isLoadingTop } = useInfiniteScroll(
  itemListRef,
  () => {
    // 連続して発火しないようにする
    itemListRef.value?.scrollTo(0, itemListRef.value.scrollTop + distanceTop + 1);

    items.value.unshift({ id: firstItem.value.id - 1 });
    items.value.unshift({ id: firstItem.value.id - 1 });
    items.value.unshift({ id: firstItem.value.id - 1 });
  },
  { distance: distanceTop, direction: 'top' }
);

const distanceBottom = 10;
const { isLoading: isLoadingBottom } = useInfiniteScroll(
  itemListRef,
  () => {
    // 連続して発火しないようにする
    itemListRef.value?.scrollTo(0, itemListRef.value.scrollTop - distanceBottom - 1);

    items.value.push({ id: lastItem.value.id + 1 });
    items.value.push({ id: lastItem.value.id + 1 });
    items.value.push({ id: lastItem.value.id + 1 });
  },
  { distance: distanceBottom, direction: 'bottom' }
);

const firstItem = computed(() => {
  return items.value.find((item) => item.id === minId.value)!;
});

const lastItem = computed(() => {
  return items.value.find((item) => item.id === maxId.value)!;
});

const minId = computed(() => {
  return Math.min(...items.value.map((item) => item.id));
});

const maxId = computed(() => {
  return Math.max(...items.value.map((item) => item.id));
});
</script>
