<template>
  <page :center-vertically="false">
    <template #title>Subscription Payment</template>
    <page-content>
      <div :class="$style.content">
        <checkout-title>Payment Details</checkout-title>

        <slot name="description">
          <checkout-description>
            <div :class="$style.description">
              <span :class="$style.text">
                Payments are safely processed with
              </span>
              <braintree-icon :class="$style.braintree" />
            </div>
          </checkout-description>
        </slot>

        <loader v-if="loading" :class="$style.loader" />
        <div v-if="userInteraction">
          <slot />
        </div>
        <pending-display
          v-if="isPending"
          :message="text.message"
          :title="text.title"
        />
        <error-display
          v-else-if="isFailure"
          :message="text.message"
          :title="text.title"
        >
          <div v-if="text.closable" :class="$style.close">
            <selection-button :selected="false" @click="close">
              OK
            </selection-button>
          </div>
        </error-display>
        <success-display
          v-else-if="isSuccess"
          :message="text.message"
          :title="text.title"
        >
          <div :class="$style['action-wrapper']">
            <nuxt-link :class="$style.action" to="/home">
              Manage Premium
            </nuxt-link>
          </div>
        </success-display>
      </div>
    </page-content>
  </page>
</template>

<script lang="ts">
import { computed, defineComponent, PropType } from '@nuxtjs/composition-api'
import { get, toRefs } from '@vueuse/core'
import { IdleStep, PaymentStep, StepType } from '~/types'

export default defineComponent({
  name: 'PaymentFrame',
  props: {
    loading: {
      required: true,
      type: Boolean,
    },
    step: {
      required: true,
      type: Object as PropType<PaymentStep>,
    },
  },
  emits: ['close'],
  setup(props, { emit }) {
    const { step } = toRefs(props)
    const useType = (type: StepType | IdleStep) => {
      return computed(() => get(step).type === type)
    }
    const text = computed(() => {
      const currentStep = get(step)
      if (currentStep.type === 'idle') {
        return {
          title: '',
          message: '',
        }
      }
      return {
        title: currentStep.title,
        message: currentStep.message,
        closable: currentStep.closeable,
      }
    })

    const close = () => {
      emit('close')
    }

    return {
      text,
      isPending: useType('pending'),
      isSuccess: useType('success'),
      isFailure: useType('failure'),
      userInteraction: useType('idle'),
      close,
    }
  },
})
</script>

<style lang="scss" module>
.loader {
  min-height: 400px;
}

.braintree {
  @apply ml-1;

  width: 120px;
}

.text {
  @apply pt-0.5;
}

.description {
  @apply flex flex-row;
}

.content {
  @apply p-0 w-full;
}

.action {
  @apply text-primary3 text-center mt-3 mb-1;
}

.action-wrapper {
  @apply flex flex-row justify-center;
}

.close {
  @apply flex flex-row justify-center mt-4;
}
</style>
