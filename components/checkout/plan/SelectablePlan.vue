<template>
  <div
    :class="{
      [$style.plan]: true,
      [$style.selected]: selected,
    }"
    @click="click"
  >
    <check-mark class="-mr-12" :selected="selected" mt="-18px" />
    <div
      :class="{
        [$style.emphasis]: true,
        [$style.name]: true,
      }"
    >
      {{ name }}
    </div>
    <div
      :class="{
        [$style.filler]: true,
        [$style.for]: true,
      }"
    >
      for *
    </div>
    <div :class="$style.emphasis">{{ price }}€</div>
    <div
      :class="{
        [$style.filler]: true,
        [$style.monthly]: true,
      }"
    >
      per month
    </div>
    <div :class="$style.total">Total: {{ totalPrice }}€</div>
    <selection-button :class="$style.button" :selected="selected">
      Choose Plan
    </selection-button>
    <div v-if="plan.discount" :class="$style.discount">
      Save {{ plan.discount }}%
    </div>
  </div>
</template>

<script lang="ts">
import {
  computed,
  defineComponent,
  PropType,
  toRefs,
} from '@nuxtjs/composition-api'
import { get } from '@vueuse/core'
import { Plan } from '~/types'
import { getPlanName } from '~/utils/plans'

export default defineComponent({
  name: 'SelectablePlan',
  props: {
    plan: {
      required: true,
      type: Object as PropType<Plan>,
    },
    selected: {
      required: true,
      type: Boolean,
    },
  },
  emits: ['click'],
  setup(props, { emit }) {
    const { plan } = toRefs(props)

    const name = computed(() => getPlanName(get(plan).months))
    const totalPrice = computed(() => get(plan).priceFiat)
    const price = computed(() => {
      const { months, priceFiat } = get(plan)
      return (parseFloat(priceFiat) / months).toFixed(2)
    })

    const click = () => {
      emit('click')
    }
    return {
      click,
      name,
      price,
      totalPrice,
    }
  },
})
</script>

<style lang="scss" module>
.small {
  font-size: 16px;
  line-height: 19px;
  letter-spacing: 0;
}

.plan {
  @apply border border-solid border flex flex-col items-center w-full h-full p-8 cursor-pointer;

  background: 0 0 no-repeat padding-box;
  border-radius: 4px;
  border: 1px solid;

  &.selected {
    background-color: #fff;
    border-color: #da4e24;
    box-shadow: 0 4px 8px #0003;
  }

  &:not(.selected) {
    background-color: #f0f0f0;
    border-color: #d2d2d2;
  }
}

.emphasis {
  @apply font-sans font-bold;

  font-size: 28px;
  line-height: 33px;
  letter-spacing: 0;
  color: #212529;
}

.for {
  margin-top: 8px;
  margin-bottom: 8px;
}

.monthly {
  margin-top: 8px;
  margin-bottom: 24px;
}

.filler {
  @apply font-sans;

  color: #545454;
  opacity: 1;

  @extend .small;
}

.total {
  font-weight: bold;
  color: #212529;

  @extend .small;
}

.discount {
  color: #878787;

  @extend .small;
}

.button {
  margin-top: 16px;
  margin-bottom: 8px;
}
</style>
