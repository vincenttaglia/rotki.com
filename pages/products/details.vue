<template>
  <products-detail-page />
</template>

<script lang="ts">
import { defineComponent, useMeta } from '@nuxtjs/composition-api'
import { commonAttrs, getMetadata } from '~/utils/metadata'
import { useRuntimeConfig } from '~/composables/utils'

const title = 'premium subscription features | rotki'
const description = 'Features included with a premium rotki subscription'

export default defineComponent({
  setup() {
    const config = useRuntimeConfig()
    const baseUrl = config.baseUrl
    useMeta({
      title,
      meta: getMetadata(title, description, `${baseUrl}/products/`, baseUrl),
      ...commonAttrs(),
    })
  },
  head: {},
})
</script>
