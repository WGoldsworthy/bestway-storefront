<template>
  <router-link :to="localizedRoute('/')" :title="$t('Home Page')" class="no-underline inline-flex">
    <img
      :width="width"
      :height="height"
      src="/assets/logo.svg"
      alt="Vuestore logo"
      class="headerImage"
    >
  </router-link>
</template>

<script>
export default {
  props: {
    width: {
      type: [String, Number],
      required: true
    },
    height: {
      type: [String, Number],
      required: true
    }
  }
}
</script>

<style>

@media screen and (max-width: 700px) {
  .headerImage {
    position: relative;
    top: 0px;
  }
}

</style>