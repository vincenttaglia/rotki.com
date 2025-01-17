<template>
  <tooltip>
    <template #activator>
      <button :class="$style.button" @click="copyToClipboard">
        <copy />
      </button>
    </template>

    <span v-if="copied">Copied to clipboard</span>
    <span v-else> Copy to clipboard </span>
  </tooltip>
</template>

<script lang="ts">
import { defineComponent, ref, toRefs } from '@nuxtjs/composition-api'
import { set, useClipboard, useTimeoutFn } from '@vueuse/core'

export default defineComponent({
  name: 'CopyButton',
  props: {
    value: {
      required: true,
      type: String,
    },
  },
  setup(props) {
    const { value } = toRefs(props)
    const copied = ref(false)
    const { start, stop } = useTimeoutFn(() => set(copied, false), 4000)
    const { copy } = useClipboard({ source: value })

    const copyToClipboard = () => {
      stop()
      copy()
      set(copied, true)
      start()
    }

    return {
      copyToClipboard,
      copied,
    }
  },
})
</script>

<style lang="scss" module>
.button {
  @apply hover:bg-shade1 rounded-2xl focus:outline-none;

  padding: 8px;
}

.ripple {
  position: absolute;
  border-radius: 50%;
  transform: scale(0);
  animation: ripple 600ms linear;
  background-color: rgba(255, 255, 255, 0.7);
}

@keyframes ripple {
  to {
    transform: scale(4);
    opacity: 0;
  }
}
</style>
