<template>
  <svg :style="{ width, height }" viewBox="0 0 24 24">
    <path d="M13 14H11V9H13M13 18H11V16H13M1 21H23L12 2L1 21Z" fill="white" />
  </svg>
</template>

<script lang="ts">
import { defineComponent } from '@nuxtjs/composition-api'

export default defineComponent({
  name: 'AlertIcon',
  props: {
    width: {
      required: false,
      type: String,
      default: '24px',
    },
    height: {
      required: false,
      type: String,
      default: '24px',
    },
  },
})
</script>
