<template>
  <div>
    <PremiumPlaceholder v-if="!canUsePremium" :class="$style.purchase" />
    <data-table
      v-if="subscriptions.length > 0"
      :headers="headers"
      :items="subscriptions"
    >
      <template #title>Subscription History</template>
      <template #item="{ item }">
        <td :class="$style.td">
          {{ item.planName }}
        </td>
        <td :class="$style.td">
          <div :class="$style.text">
            {{ item.createdDate }}
          </div>
        </td>
        <td :class="$style.td">
          <div :class="$style.text">
            {{ item.nextActionDate }}
          </div>
        </td>
        <td :class="$style.td">
          <div :class="$style.text">
            {{ item.nextBillingAmount }}
          </div>
        </td>
        <td :class="$style.td">
          <span
            :class="{
              [$style.status]: true,
              [$style.active]: item.status === 'Active',
              [$style.cancelled]: item.status === 'Cancelled',
              [$style.pending]: item.status === 'Pending',
              [$style.pastDue]: item.status === 'Past Due',
            }"
          >
            {{ item.status }}
          </span>
        </td>
        <td :class="$style.action">
          <div v-if="displayActions(item)">
            <cancel-subscription
              v-if="hasAction(item, 'cancel')"
              :subscription="item"
            />
            <nuxt-link
              v-if="hasAction(item, 'renew')"
              :class="$style.actionButton"
              :to="renewLink"
            >
              Renew
            </nuxt-link>
          </div>
          <div v-else>None</div>
        </td>
      </template>
    </data-table>
    <error-notification :visible="!!cancellationError">
      <template #title>Cancellation Failure</template>
      <template #description>
        {{ cancellationError }}
      </template>
    </error-notification>
  </div>
</template>

<script lang="ts">
import {
  computed,
  defineComponent,
  onMounted,
  onUnmounted,
  ref,
  watch,
} from '@nuxtjs/composition-api'
import { get, set, useIntervalFn } from '@vueuse/core'
import { storeToRefs } from 'pinia'
import CancelSubscription from '~/components/account/home/CancelSubscription.vue'
import { DataTableHeader } from '~/components/common/DataTable.vue'
import { useMainStore } from '~/store'
import PremiumPlaceholder from '~/components/account/home/PremiumPlaceholder.vue'
import { Subscription } from '~/types'

const subHeaders: DataTableHeader[] = [
  { text: 'Plan', value: '' },
  { text: 'Created', value: '', sortable: true },
  { text: 'Next Billing', value: '', sortable: true },
  { text: 'Cost in € per period', value: '', sortable: true },
  { text: 'Status', value: '' },
  { text: 'Actions', value: '', className: 'text-right' },
]

export default defineComponent({
  name: 'Subscriptions',
  components: { PremiumPlaceholder, CancelSubscription },
  setup() {
    const store = useMainStore()
    const { account, cancellationError } = storeToRefs(store)
    const renewLink = ref<{ path: string; query: Record<string, string> }>({
      path: '/checkout/payment-method',
      query: {},
    })

    const subscriptions = computed(() => {
      const userAccount = get(account)
      if (!userAccount) {
        return []
      }
      return userAccount.subscriptions
    })

    const pending = computed(() =>
      get(subscriptions).filter((sub) => sub.pending)
    )

    const { pause, resume, isActive } = useIntervalFn(
      async () => await store.getAccount(),
      60000
    )

    watch(pending, (pending) => {
      if (pending.length === 0) {
        pause()
      } else if (!get(isActive)) {
        resume()
      }
    })

    onUnmounted(() => pause())

    const canUsePremium = computed(() => {
      const arePending = get(pending)
      return get(account)?.canUsePremium || arePending.length > 0
    })

    onMounted(async () => {
      const renewableSubscriptions = get(subscriptions).filter(({ actions }) =>
        actions.includes('renew')
      )
      if (renewableSubscriptions.length) {
        const subscription = renewableSubscriptions[0]
        const result = await store.checkPendingCryptoPayment(
          subscription.identifier
        )
        if (result.isError || !result.result.pending) {
          set(renewLink, {
            path: '/checkout/payment-method',
            query: {
              p: subscription.durationInMonths.toString(),
              id: subscription.identifier,
            },
          })
        } else {
          set(renewLink, {
            path: '/checkout/pay/crypto',
            query: {
              p: subscription.durationInMonths.toString(),
              id: subscription.identifier,
              c: result.result.currency ?? '',
            },
          })
        }
      }
    })

    const hasAction = (sub: Subscription, action: 'renew' | 'cancel') => {
      if (action === 'cancel') {
        return sub.status !== 'Pending' && sub.actions.includes('cancel')
      } else if (action === 'renew') {
        return sub.actions.includes('renew')
      }
      return false
    }

    const displayActions = (sub: Subscription) => {
      return hasAction(sub, 'renew') || hasAction(sub, 'cancel')
    }

    return {
      hasAction,
      displayActions,
      headers: subHeaders,
      subscriptions,
      renewLink,
      canUsePremium,
      cancellationError,
    }
  },
})
</script>

<style lang="scss" module>
@import '~assets/css/media';

.td {
  @apply px-6 py-4 whitespace-nowrap;
}

.text {
  @apply text-sm text-gray-500;
}

.action {
  @apply px-6 py-4 whitespace-nowrap text-right text-sm font-medium;
}

.actionButton {
  @apply text-indigo-600 hover:text-indigo-900;
}

.status {
  @apply px-2 inline-flex text-xs leading-5 font-semibold rounded-full;
}

.active {
  @apply bg-green-100 text-green-800;
}

.cancelled {
  @apply bg-red-100 text-red-600;
}

.pending {
  @apply bg-yellow-100 text-yellow-600;
}

.pastDue {
  @apply bg-yellow-300 text-yellow-800;
}

.purchase {
  @apply mb-8;
}
</style>
