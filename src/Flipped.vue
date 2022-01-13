<script>
import { getCurrentInstance, onMounted, inject } from "vue";
export default {
  name: "flipped",
  props: {
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
  },
  emits: ["on-start", "on-complete"],
  setup(props, { emit, slots }) {
    const addFlippedElement = inject('addFlippedElement')
    const addInvertedElement = inject('addInvertedElement')
    onMounted(() => {
    const instance = getCurrentInstance();
      if (props.flipId) {
        addFlippedElement({
          element: instance.vnode.el,
          flipId: props.flipId,
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
          element: instance.vnode.el,
          parent: instance.parent.vnode.el,
          opacity: props.opacity,
          scale: props.scale,
          translate: props.translate
        });
      }
    })

    return () => slots.default()[0]
  },
};
</script>
