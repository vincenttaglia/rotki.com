<template>
  <div :class="$style.row">
    <div :class="$style.column">
      <nuxt-link
        v-if="landing"
        :class="$style.link"
        to="/#features"
        rel="noreferrer"
      >
        Features
      </nuxt-link>
      <nuxt-link v-else :class="$style.link" to="/" rel="noreferrer">
        Home
      </nuxt-link>
    </div>
    <div :class="$style.column">
      <a
        :class="$style.link"
        href="https://rotki.readthedocs.io/en/latest/index.html"
        rel="noreferrer"
        target="_blank"
      >
        Documentation
      </a>
    </div>
    <div :class="$style.column">
      <a
        :class="$style.link"
        href="https://blog.rotki.com/"
        rel="noreferrer"
        target="_blank"
      >
        Blog
      </a>
    </div>
    <div :class="$style.column">
      <nuxt-link :class="$style.link" to="/home"> Manage Premium</nuxt-link>
    </div>
    <div :class="$style.column">
      <nuxt-link :class="$style.link" to="/jobs"> Jobs </nuxt-link>
    </div>
    <div :class="$style.column">
      <a :class="$style.link" href="mailto:info@rotki.com" target="_blank">
        Contact
      </a>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from '@nuxtjs/composition-api'

export default defineComponent({
  name: 'Links',
  props: {
    landing: { required: false, default: false, type: Boolean },
  },
})
</script>

<style lang="scss" module>
@import '~assets/css/media.scss';

.row {
  @apply flex flex-row justify-around;

  @include for-size(phone-only) {
    @apply flex-wrap justify-center text-center;

    max-width: 100%;
  }
}

.column {
  @apply flex flex-col font-sans uppercase justify-center text-primary2;

  margin-left: 16px;
  margin-right: 16px;

  @include for-size(phone-only) {
    width: 100%;
    margin: 0 auto;
  }
}

.link {
  @apply hover:text-shade8;

  @include for-size(phone-only) {
    @apply py-2;
  }
}
</style>
