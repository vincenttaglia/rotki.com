<template>
  <page wide>
    <template #title> Account Management</template>
    <template #links>
      <link-text>
        <span @click="logout">Logout</span>
      </link-text>
    </template>
    <heading :class="$style.header"> Welcome {{ username }}</heading>
    <subscriptions :class="$style.category" />
    <payments :class="$style.category" />
    <api-keys v-if="premium" :class="$style.category" />
    <account-details :class="$style.category" />
    <danger-zone :class="$style.category" />
  </page>
</template>

<script lang="ts">
import {
  computed,
  defineComponent,
  onMounted,
  toRefs,
  useRouter,
} from '@nuxtjs/composition-api'
import { get } from '@vueuse/core'
import { useMainStore } from '~/store'

export default defineComponent({
  name: 'Overview',
  setup() {
    const store = useMainStore()
    // pinia#852
    const { account } = toRefs(store)
    const premium = computed(() => {
      return get(account)?.canUsePremium ?? false
    })

    const username = computed(() => {
      return get(account)?.username
    })

    onMounted(async () => await store.getAccount())

    const router = useRouter()
    const logout = async () => {
      await store.logout(true)
      router.push('/login')
    }
    return {
      username,
      premium,
      logout,
    }
  },
})
</script>

<style lang="scss" module>
.category {
  @apply mt-8;
}

.header {
  @apply pb-2;
}
</style>
