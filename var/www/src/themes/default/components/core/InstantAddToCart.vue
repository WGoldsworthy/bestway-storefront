<template>
  <instant-add-button
    @click.native.prevent="addToCart(product)"
    :disabled="isProductDisabled"
    data-testid="addToCart"
  />
</template>

<script>
import { formatProductMessages } from '@vue-storefront/core/filters/product-messages'
import focusClean from 'theme/components/theme/directives/focusClean'
import InstantAddButton from 'theme/components/theme/InstantAddButton.vue'
import { AddToCart } from '@vue-storefront/core/modules/cart/components/AddToCart'

export default {
  mixins: [AddToCart],
  directives: { focusClean },
  components: { InstantAddButton },
  methods: {
    onAfterRemovedVariant () {
      this.$forceUpdate()
    }
  },
  computed: {
    isProductDisabled () {
      return this.disabled || formatProductMessages(this.product.errors) !== ''
    }
  },
  beforeMount () {
    this.$bus.$on('product-after-removevariant', this.onAfterRemovedVariant)
  },
  beforeDestroy () {
    this.$bus.$off('product-after-removevariant')
  }
}
</script>
