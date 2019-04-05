<template>
  <button
    class="material-icons"
    data-testid="subscribeSubmit"
    :disabled="disabled"
  >
    shopping_cart
  </button>
</template>

<script>
import focusClean from 'theme/components/theme/directives/focusClean'
export default {
  name: 'InstantAddButton',
  directives: { focusClean },
  props: {
    type: {
      type: String,
      required: false,
      default: 'button'
    },
    disabled: {
      type: Boolean,
      required: false,
      default: false
    }
  }
}
</script>

<style lang="scss" scoped>
  button {
    border: 0;
    vertical-align: middle;
  }
</style>
