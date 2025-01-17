<template>
  <button
    :class="{
      [$style.button]: true,
      [$style.selected]: selected,
    }"
    :disabled="disabled"
    @click="click"
  >
    <span
      :class="{
        [$style.text]: true,
        [$style.selected]: selected,
      }"
    >
      <slot />
    </span>
  </button>
</template>

<script lang="ts">
import { defineComponent } from '@nuxtjs/composition-api'

export default defineComponent({
  name: 'SelectionButton',
  props: {
    selected: {
      required: true,
      type: Boolean,
    },
    disabled: {
      required: false,
      type: Boolean,
      default: false,
    },
  },
  emits: ['click'],
  setup(_, { emit }) {
    const click = () => {
      emit('click')
    }
    return {
      click,
    }
  },
})
</script>

<style lang="scss" module>
$color: #da4e24;

.text {
  @apply font-sans font-bold;

  line-height: 19px;
  font-size: 16px;
  letter-spacing: 0;

  &:not(.selected) {
    color: $color;
  }

  &.selected {
    color: #fff;
  }
}

.button {
  background: 0 0 no-repeat padding-box;
  border: 1px solid $color;
  @apply relative overflow-hidden rounded-full py-1.5 px-4;

  &::after {
    content: '';
    width: 200px;
    height: 200px;
    @apply absolute z-10 left-1/2 top-1/2 bg-black bg-opacity-10 transform -translate-x-1/2 -translate-y-1/2 transition-all duration-300 scale-0 rounded-full;
  }

  &:hover::after {
    @apply scale-100;
  }

  &:disabled {
    background-color: #f0f0f0 !important;
    border-color: #f0f0f0;

    & .text {
      color: #878787 !important;
    }

    @apply hover:scale-100;

    &::after {
      content: unset;
    }
  }

  &.selected {
    background-color: $color;

    @apply hover:scale-100;

    &::after {
      content: unset;
    }
  }

  &:focus {
    @apply outline-none;

    color: rgba($color, 0.7);
  }
}
</style>
