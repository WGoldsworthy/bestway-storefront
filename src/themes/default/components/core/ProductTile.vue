<template>
  <div
    class="product align-center w-100 pb20"
    v-observe-visibility="visibilityChanged"
  >
    <router-link
      class="block no-underline product-link"
      :to="localizedRoute({
        name: product.type_id + '-product',
        params: {
          parentSku: product.parentSku ? product.parentSku : product.sku,
          slug: product.slug,
          childSku: product.sku
        }
      })"
      data-testid="productLink"
    >
      <div
        class="product-image relative bg-cl-secondary"
        :class="[{ sale: labelsActive && isOnSale }, { new: labelsActive && isNew }, {'product-image--loaded': imageLoaded}]">
        <img
          class="product-image__content"
          :alt="product.name"
          :src="thumbnailObj.src"
          height="300"
          width="310"
          data-testid="productImage"
          @load="imageLoaded = true"
        >
        <div class="product-image-overlay">
          <span v-if="product.rsp">RSP: {{ product.rsp | price }} </span><br>
          <span v-if="product.pack_size">{{ product.pack_size }} x</span>
          <span v-if="product.number">{{ product.number }} units</span>
        </div> 
      </div>

      <p class="mb0 cl-accent mt10" v-if="!onlyImage">
        {{ product.name | htmlDecode }} 
      </p>
    </router-link>
    <div v-if="currentUser">
      <span
        class="price-original mr5 lh30 cl-secondary"
        v-if="product.special_price && parseFloat(product.originalPriceInclTax) > 0 && !onlyImage"
      >
        {{ product.originalPriceInclTax | price }} 
      </span>

      <span 
        class="price-tier mr5 cl-secondary"
        v-if="typeof product.tier_prices !== 'undefined' && product.tier_prices.length > 0"
      >
        £{{ getTierPrice() }}
      </span>

      <span
        class="price-special lh30 cl-accent weight-700"
        v-if="product.special_price && parseFloat(product.special_price) > 0 && !onlyImage"
      >
        {{ product.priceInclTax | price }}
      </span>

      <span
        class="lh30 cl-secondary"
        v-if="!product.special_price && parseFloat(product.priceInclTax) > 0 && !onlyImage && typeof product.tier_prices == 'undefined'"
      >
        {{ product.priceInclTax | price }}
      </span>
      <base-input-number
        :name="$t('Quantity')"
        v-model="product.qty"
        :min="1"
        @blur="$v.$touch()"
      >
      </base-input-number>
      <instant-add-to-cart :product="product" />
    </div>
    <span v-if="!currentUser">
      <button href="#" @click.prevent="gotoAccount" class="loginButton">
      {{ $t('Login to view prices') }}
      </button>
    </span>
  </div>
</template>

<script>
import rootStore from '@vue-storefront/core/store'
import InstantAddToCart from 'theme/components/core/InstantAddToCart.vue'
import BaseInputNumber from 'theme/components/core/blocks/Form/BaseInputNumber'
import { ProductTile } from '@vue-storefront/core/modules/catalog/components/ProductTile.ts'
import {mapState} from 'vuex'

export default {
  mixins: [ProductTile],
  data () {
    return {
      imageLoaded: false,
    }
  },
  props: {
    labelsActive: {
      type: Boolean,
      requred: false,
      default: true
    },
    onlyImage: {
      type: Boolean,
      required: false,
      default: false
    }
  },
  components: {
    InstantAddToCart,
    BaseInputNumber
  },
  computed: {
    ...mapState({
      currentUser: state => state.user.current
    })
  },
  methods: {
    gotoAccount () {
      this.$bus.$emit('modal-toggle', 'modal-signup')
    },
    onProductPriceUpdate (product) {
      if (product.sku === this.product.sku) {
        Object.assign(this.product, product)
      }
    },
    getTierPrice () {
      if (typeof this.product.tier_prices !== 'undefined') {
        var parent = this;
        var customerTier = this.product.tier_prices.find(function(element) {return element.customer_group_id == parent.currentUser.group_id})
        if (typeof customerTier !== 'undefined') {
          return customerTier.value;
        } else {
          return this.product.priceInclTax;
        }
      }
    },
    visibilityChanged (isVisible, entry) {
      if (isVisible) {
        if (rootStore.state.config.products.configurableChildrenStockPrefetchDynamic && rootStore.products.filterUnavailableVariants) {
          const skus = [this.product.sku]
          if (this.product.type_id === 'configurable' && this.product.configurable_children && this.product.configurable_children.length > 0) {
            for (const confChild of this.product.configurable_children) {
              const cachedItem = rootStore.state.stock.cache[confChild.id]
              if (typeof cachedItem === 'undefined' || cachedItem === null) {
                skus.push(confChild.sku)
              }
            }
            if (skus.length > 0) {
              rootStore.dispatch('stock/list', { skus: skus }) // store it in the cache
            }
          }
        }
      }
    }
  },
  beforeMount () {
    this.$bus.$on('product-after-priceupdate', this.onProductPriceUpdate)
  },
  beforeDestroy () {
    this.$bus.$off('product-after-priceupdate', this.onProductPriceUpdate)
  }
}
</script>

<style lang="scss" scoped>
@import '~theme/css/animations/transitions';
@import '~theme/css/variables/colors';
@import '~theme/css/helpers/functions/color';

$bg-secondary: color(secondary, $colors-background);
$border-secondary: color(secondary, $colors-border);
$color-white: color(white);

.product {
  @media (max-width: 767px) {
    padding-bottom: 10px;
  }
}

.price-original {
  text-decoration: line-through;
}

%label {
  position: absolute;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 40px;
  height: 40px;
  background-color: $border-secondary;
  text-transform: uppercase;
  color: $color-white;
  font-size: 12px;
}

.product-image-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 0;
  width: 100%;
  opacity: 0;
  margin: 0;
  transition: .5s ease;
  background-color: #EEE;
  padding: 10px;
  font-size: 0.9em;
  text-align: left;
}

.product-image {
  width: 100%;
  overflow: hidden;
  max-height: 300px;
  height: 100%;
  min-height: 155px;
  display: flex;
  align-items: flex-end;
  background-image: url('/assets/placeholder.svg');
  background-repeat: no-repeat;
  background-position: center;
  background-size: 60% auto;
  padding: 0;

  @media (min-width: 768px) {
    min-height: 190px;
  }
  @media (min-width: 1200px) {
    min-height: 300px;
  }

  &__content {
    display: none;
  }

  &--loaded {
    background-image: none;

    .product-image__content {
      display: block;
    }
  }

  &:hover {
    img {
      opacity: 1;
      transform: scale(1.1);
    }

    .product-image-overlay {
      opacity: 0.9;
      height: 20%;
    }

    &.sale::after,
    &.new::after {
      opacity: 0.8;
    }
  }

  img {
    max-height: 100%;
    max-width: 100%;
    width: auto;
    height: auto;
    margin: auto;
    mix-blend-mode: darken;
    opacity: 0.8;
    transform: scale(1);
    transition: 0.3s opacity $motion-main, 0.3s transform $motion-main;

    &[lazy="loaded"] {
      animation: products-loaded;
      animation-duration: 0.3s;
    }

    @keyframes products-loaded {
      from {
        opacity: 0;
      }
      to {
        opacity: 0.8;
      }
    }
  }

  &.sale {
    &::after {
      @extend %label;
      content: 'Sale';
    }
  }

  &.new {
    &::after {
      @extend %label;
      content: 'New';
    }
  }
  .loginButton {
    position: absolute;
    bottom:0;
  }
}
</style>
