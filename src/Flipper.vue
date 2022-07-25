<script setup>
import { Flipper } from "flip-toolkit";
import { onBeforeUpdate, onMounted, watch, nextTick, ref, provide } from '@vue/runtime-core'

const props = defineProps({
  className: String,
  flipKey: [String, Number, Boolean],
  staggerConfig: Object,
  spring: {
    type: [String, Object],
    default: "noWobble"
  }
})

const flipInstance = ref(null)
const ready = ref(false)

const addFlippedElement = ({
  element,
  flipId,
  delayUntil,
  stagger,
  shouldFlip,
  shouldInvert,
  onStart,
  onComplete,
  opacity,
  scale,
  translate
}) => {
  flipInstance.value.addFlipped({
    element,
    flipId,
    delayUntil,
    stagger,
    shouldFlip,
    shouldInvert,
    onStart,
    onComplete,
    opacity,
    scale,
    translate
  });
}

const addInvertedElement = async ({ element, parent, opacity, scale, translate }) => {
  await nextTick()
  flipInstance.value.addInverted({
    element,
    parent,
    opacity,
    scale,
    translate
  });
}

provide('addFlippedElement', addFlippedElement)
provide('addInvertedElement', addInvertedElement)

const rootEl = ref()

onMounted(() => {
  flipInstance.value = new Flipper({
    element: rootEl.value,
    spring: props.spring,
    staggerConfig: props.staggerConfig || null,
  });
  ready.value = true;
})
onBeforeUpdate(() => {
  flipInstance.value.recordBeforeUpdate();
})

watch(
  () => props.flipKey,
  async (newKey, oldKey) => {
    if (newKey !== oldKey) {
      await nextTick()
      flipInstance.value.update(oldKey, newKey);
    }
  }
)

watch(
  () => props.staggerConfig,
  (oldConfig, newConfig) => {
    if (newConfig !== oldConfig) {
      flipInstance.value.staggerConfig = newConfig;
    }
  }
)
</script>

<template>
  <div :class="props.className" ref="rootEl">
    <slot v-if="ready"></slot>
  </div>
</template>
