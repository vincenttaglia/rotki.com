<template>
  <div :class="$style.wrapper">
    <div :class="$style.column">
      <div v-if="$slots.subtitle" :class="$style.subtitle">
        <slot name="subtitle" />
      </div>
      <div :class="$style.description">
        <slot />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from '@nuxtjs/composition-api'

export default defineComponent({
  name: 'FeatureDetails',
})
</script>

<style lang="scss" module>
@import '~assets/css/media';
@import '~assets/css/main';

.wrapper {
  @apply flex flex-row;

  margin-bottom: 40px;
}

.column {
  @apply flex flex-col;
}

.subtitle {
  @apply flex flex-row font-serif font-bold text-primary2;

  letter-spacing: -0.01em;
  margin-top: 58px;
  margin-bottom: 16px;

  @include text-size(25px, 46px);
}

.description {
  @apply flex flex-row font-sans text-primary2;

  @include text-size(24px, 32px);
}
</style>
