<template>
  <div class="w-100 brdr-bottom-1 bg-cl-primary brdr-cl-secondary">
    <div class="container px15">
      <ul class="main-menu-list">
        <li>
          <router-link :to="localizedRoute('/sale')">
            {{ $t('Sale') }}
          </router-link>
        </li>
        <li>Soft Drinks</li>
        <li>Bread &amp; Cakes</li>
        <li>Confectionery</li>
        <li>Chilled &amp; Fresh Food</li>
        <li>Frozen Foods</li>
        <li>Beers, Wines &amp; Spirits</li>
        <li>Non Food</li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
}
</script>

<style lang="scss" scoped>

  $brand-color-blue: #0093d8;
  $brand-color-green: #00a665;

  .main-menu-list {
    display: block;
    list-style: none;
    padding: 5px 0;
    li {
      display: inline-block;
      padding: 0 20px;
      transition: color 0.2s ease-in-out;
      &:first-child {
        padding-left: 0;
        cursor: pointer;
        &:hover {
          color: $brand-color-green;
          text-decoration: underline;
        }
      }
    }
  }
</style>
