<script setup>
import { getCurrentInstance, onMounted, inject, useSlots, useAttrs } from "@vue/runtime-core";

const props = defineProps({
  flipId: String,
  inverseFlipId: String,
  delayUntil: String,
  stagger: [String, Boolean],
  shouldFlip: Function,
  shouldInvert: Function,
  scale: {
    type: Boolean,
    default: false
  },
  opacity: {
    type: Boolean,
    default: false
  },
  translate: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(["on-start", "on-complete"])
const slots = useSlots()
const attrs = useAttrs()

const addFlippedElement = inject('addFlippedElement')
const addInvertedElement = inject('addInvertedElement')

onMounted(() => {
  const instance = getCurrentInstance();
  instance.refs['rootsEl'].forEach((el, ind) => {
    if (props.flipId) {
      addFlippedElement({
        element: el,
        flipId: props.flipId + ind,
        delayUntil: props.delayUntil,
        shouldFlip: props.shouldFlip,
        shouldInvert: props.shouldInvert,
        onStart: el => emit("on-start", { el, id: props.flipId }),
        onComplete: el => emit("on-complete", { el, id: props.flipId }),
        stagger: props.stagger,
        opacity: props.opacity,
        scale: props.scale,
        translate: props.translate
      });
    } else if (props.inverseFlipId) {
      addInvertedElement({
        element: el,
        parent: instance.vnode.el.parentElement,
        opacity: props.opacity,
        scale: props.scale,
        translate: props.translate
      });
    }
  })

})
</script>

<template>
  <template v-for="(defaultComp) in slots.default()">
    <component :is="defaultComp" ref="rootsEl" v-bind="attrs" />
  </template>
</template>