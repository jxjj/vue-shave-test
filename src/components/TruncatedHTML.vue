<template>
  <div class="shave-text" :class="{ 'shave-text--show-more': show }">
    <div ref="shavedTextContainer">
      <div v-html="html" />
    </div>

    <button @click="show = !show">Show More</button>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import { useResizeObserver } from "@vueuse/core";

import shave from "shave";

const props = withDefaults(
  defineProps<{
    html: string;
    maxHeight?: number;
  }>(),
  {
    maxHeight: 50,
  }
);

const show = ref(false);
const shavedTextContainer = ref<HTMLDivElement | null>(null);

function shaveIt() {
  if (!shavedTextContainer.value) return;

  // cast the element as a NodeList, since shave's types
  // say they don't allow a single element (even though they do!)
  shave([shavedTextContainer.value] as unknown as NodeList, props.maxHeight);

  // remove the display: none inline style from the hidden text
  // so that we can just use classes to style it
  shavedTextContainer.value
    .querySelector<HTMLElement>(".js-shave")
    ?.removeAttribute("style");
}

useResizeObserver(shavedTextContainer, shaveIt);
onMounted(shaveIt);
</script>
<style>
.js-shave {
  display: none;
}

.shave-text--show-more .js-shave-char {
  display: none;
}

.shave-text--show-more .js-shave {
  display: inline;
}
</style>
