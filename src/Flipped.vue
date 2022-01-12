<script>
import { getCurrentInstance } from "vue";
export default {
  name: "flipped",
  inject: ["addFlippedElement", "addInvertedElement"],
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
  mounted() {
    const instance = getCurrentInstance();
    if (this.flipId) {
      this.addFlippedElement({
        element: instance.vnode.el,
        flipId: this.flipId,
        delayUntil: this.delayUntil,
        shouldFlip: this.shouldFlip,
        shouldInvert: this.shouldInvert,
        onStart: el => this.$emit("on-start", { el, id: this.flipId }),
        onComplete: el => this.$emit("on-complete", { el, id: this.flipId }),
        stagger: this.stagger,
        opacity: this.opacity,
        scale: this.scale,
        translate: this.translate
      });
    } else if (this.inverseFlipId) {
      this.addInvertedElement({
        element: instance.vnode.el,
        parent: instance.parent.vnode.el,
        opacity: this.opacity,
        scale: this.scale,
        translate: this.translate
      });
    }
  },
  render() {
    return this.$slots.default()[0];
  }
};
</script>
