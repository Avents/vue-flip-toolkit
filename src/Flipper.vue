<script>
import { Flipper } from "flip-toolkit";
import { h, onBeforeUpdate, onMounted, watch, nextTick, ref, provide } from 'vue'

export default {
  name: "flipper",
  props: {
    className: String,
    flipKey: [String, Number, Boolean],
    staggerConfig: Object,
    spring: {
      type: [String, Object],
      default: "noWobble"
    }
  },
  setup(props, { slots }) {

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
        ...(delayUntil ? { delayUntil } : undefined),
        ...(stagger ? { stagger } : undefined),
        ...(shouldFlip ? { shouldFlip } : undefined),
        ...(shouldInvert ? { shouldInvert } : undefined),
        ...(onStart ? { onStart } : undefined),
        ...(onComplete ? { onComplete } : undefined),
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
      console.log(rootEl.value)
      flipInstance.value = new Flipper({
        element: rootEl.value,
        spring: props.spring,
        ...(props.staggerConfig ? { staggerConfig: props.staggerConfig } : null)
      });
      ready.value = true;
    })
    onBeforeUpdate(() => {
      flipInstance.value.recordBeforeUpdate();
    })

    watch(
      () =>  props.flipKey,
      async (newKey, oldKey) => {
        if (newKey !== oldKey) {
          await nextTick()
          flipInstance.value.update(oldKey, newKey);
        }
      }
    )

    watch(
      () =>  props.staggerConfig,
      (oldConfig, newConfig) => {
        if (newConfig !== oldConfig) {
          flipInstance.value.staggerConfig = newConfig;
        }
      }
    )

    return () => {
      return h('div',
        {
          class: props.className,
          ref: rootEl
        },
        {
          default: () => {
            if (ready.value) {
              return slots.default()
            }
          }
        }
      )
    }

  },
};
</script>
