<template>
  <div class="pt20">
    <div class="row pl20">
      <div class="col-xs-1 col-sm-2 col-md-1">
        <div
          class="number-circle lh35 cl-white brdr-circle align-center weight-700"
          :class="{ 'bg-cl-th-accent' : isActive || isFilled, 'bg-cl-tertiary' : !isFilled && !isActive }"
        >
          2
        </div>
      </div>
      <div class="col-xs-11 col-sm-9 col-md-11">
        <div class="row mb15">
          <div class="col-xs-12 col-md-7" :class="{ 'cl-bg-tertiary' : !isFilled && !isActive }">
            <h3 class="m0 mb5">
              {{ $t('Shipping') }}
            </h3>
          </div>
          <div class="col-xs-12 col-md-5 pr30">
            <div class="lh30 flex end-lg" v-if="isFilled && !isActive">
              <a href="#" class="cl-tertiary flex" @click.prevent="edit">
                <span class="pr5">
                  {{ $t('Edit shipping') }}
                </span>
                <i class="material-icons cl-tertiary">edit</i>
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="row pl20" v-if="isActive">
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-11 col-sm-9 col-md-10">
        <div class="row">
          <div class="delivery-address-form">
            <base-checkbox
              v-if="currentUser && hasShippingDetails()"
              class="col-xs-12 mb25"
              id="shipToMyAddressCheckbox"
              @click="useMyAddress"
              v-model="shipToMyAddress"
            >
              {{ $t('Ship to my default address') }}
            </base-checkbox>

            <base-input
              class="col-xs-12 col-sm-6 mb25"
              type="text"
              name="first-name"
              :placeholder="$t('First name *')"
              v-model.trim="shipping.firstName"
              @blur="$v.shipping.firstName.$touch()"
              autocomplete="given-name"
              :validations="[
                {
                  condition: $v.shipping.firstName.$error && !$v.shipping.firstName.required,
                  text: $t('Field is required')
                },
                {
                  condition: !$v.shipping.firstName.minLength,
                  text: $t('Name must have at least 2 letters.')
                }
              ]"
            />

            <base-input
              class="col-xs-12 col-sm-6 mb25"
              type="text"
              name="last-name"
              :placeholder="$t('Last name *')"
              v-model.trim="shipping.lastName"
              @blur="$v.shipping.lastName.$touch()"
              autocomplete="family-name"
              :validations="[{
                condition: $v.shipping.lastName.$error && !$v.shipping.lastName.required,
                text: $t('Field is required')
              }]"
            />

            <base-input
              class="col-xs-12 mb25"
              type="text"
              name="street-address"
              :placeholder="$t('Street name *')"
              v-model.trim="shipping.streetAddress"
              @blur="$v.shipping.streetAddress.$touch()"
              autocomplete="address-line1"
              :validations="[{
                condition: $v.shipping.streetAddress.$error && !$v.shipping.streetAddress.required,
                text: $t('Field is required')
              }]"
            />

            <base-input
              class="col-xs-12 mb25"
              type="text"
              name="apartment-number"
              :placeholder="$t('House/Apartment number *')"
              v-model.trim="shipping.apartmentNumber"
              @blur="$v.shipping.apartmentNumber.$touch()"
              autocomplete="address-line2"
              :validations="[{
                condition: $v.shipping.apartmentNumber.$error && !$v.shipping.apartmentNumber.required,
                text: $t('Field is required')
              }]"
            />

            <base-input
              class="col-xs-12 col-sm-6 mb25"
              type="text"
              name="city"
              :placeholder="$t('City *')"
              v-model.trim="shipping.city"
              @blur="$v.shipping.city.$touch()"
              autocomplete="address-level2"
              :validations="[{
                condition: $v.shipping.city.$error && !$v.shipping.city.required,
                text: $t('Field is required')
              }]"
            />

            <base-input
              class="col-xs-12 col-sm-6 mb25"
              type="text"
              name="state"
              :placeholder="$t('State / Province')"
              v-model.trim="shipping.state"
              autocomplete="address-level1"
            />

            <base-input
              class="col-xs-12 col-sm-6 mb25"
              type="text"
              name="zip-code"
              :placeholder="$t('Zip-code *')"
              v-model.trim="shipping.zipCode"
              @blur="$v.shipping.zipCode.$touch()"
              autocomplete="postal-code"
              :validations="[
                {
                  condition: $v.shipping.zipCode.$error && !$v.shipping.zipCode.required,
                  text: $t('Field is required')
                },
                {
                  condition: !$v.shipping.zipCode.minLength,
                  text: $t('Name must have at least 3 letters.')
                }
              ]"
            />

            <base-select
              class="col-xs-12 col-sm-6 mb25"
              name="countries"
              :options="countryOptions"
              :selected="shipping.country"
              :placeholder="$t('Country *')"
              :validations="[
                {
                  condition: $v.shipping.country.$error && !$v.shipping.country.required,
                  text: $t('Field is required')
                }
              ]"
              v-model="shipping.country"
              autocomplete="country-name"
              @blur="$v.shipping.country.$touch()"
              @change="$v.shipping.country.$touch(); changeCountry();"
            />

            <base-input
              class="col-xs-12 mb25"
              type="text"
              name="phone-number"
              :placeholder="$t('Phone Number')"
              v-model.trim="shipping.phoneNumber"
              autocomplete="tel"
            />
          </div>

          <h4 class="col-xs-12">
            {{ $t('Shipping method') }}
          </h4>
          <div class="col-md-6">
            <button
              class="delivery-types"
              @click="isDateSectionActive = !isDateSectionActive"
              :class="isDateSectionActive ? 'active' : ''"
            >
              Click &amp; Collect
            </button>
          </div>
          <div class="col-md-6">
            <button class="delivery-types" disabled>Deliver Home</button>
          </div>
          <span class="validation-error" v-if="$v.shipping.shippingMethod.$error && !$v.shipping.shippingMethod.required">
            {{ $t('Field is required') }}
          </span>
        </div>
      </div>
    </div>
    <div
      class="row cnc-depots"
      v-if="isDateSectionActive"
    >
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-12 col-sm-9 col-md-11">
        <h4 class="col-xs-12">
          {{ $t('Select a Depot') }}
        </h4>
        <div class="col-md-11">
          <v-select
            v-model="defaultVal"
            :options="depots"
            @input="changeLabel"
          />
          <div>
            <p>Select a date</p>
            <button
              class="date-btn"
              v-for="(date, index) in dates"
              :key="index"
              @click="dateSelected(date)"
              :class="{ 'active': date === selectedDate }"
            >
              {{ date }}
            </button>
          </div>
          <div v-show="isDateSelected">
            <h4>Available time for your selected day - {{ selectedDate }}</h4>
            <button
              v-for="(time, index) in times"
              :key="index"
              class="time-btn"
              @click="timeSelected(time)"
              :class="{ 'active': time === selectedTime }"
            >
              {{ time }}
            </button>
          </div>
        </div>
      </div>
    </div>
    <div class="row" v-if="isActive">
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-12 col-sm-9 col-md-11">
        <div class="row">
          <div class="col-xs-12 col-md-8 my30 px20">
            <button-full
              data-testid="shippingSubmit"
              @click.native="sendDataToCheckout"
              :disabled="!isTimeSelected"
            >
              {{ $t('Continue to payment') }}
            </button-full>
          </div>
        </div>
      </div>
    </div>
    <div class="row pl20" v-if="!isActive && isFilled">
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-12 col-sm-9 col-md-11">
        <div class="row fs16 mb35">
          <div class="col-xs-12 h4" data-testid="shippingAddressSummary">
            <p>
              {{ shipping.streetAddress }} {{ shipping.apartmentNumber }}
            </p>
            <p>
              {{ shipping.city }} {{ shipping.zipCode }}
            </p>
            <p>
              <span v-if="shipping.state">{{ shipping.state }}, </span>
              <span>{{ getCountryName() }}</span>
            </p>
            <div v-if="shipping.phoneNumber">
              <span class="pr15">{{ shipping.phoneNumber }}</span>
              <tooltip>{{ $t('Phone number may be needed by carrier') }}</tooltip>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { required, minLength } from 'vuelidate/lib/validators'
import { Shipping } from '@vue-storefront/core/modules/checkout/components/Shipping'

import BaseCheckbox from 'theme/components/core/blocks/Form/BaseCheckbox'
import BaseInput from 'theme/components/core/blocks/Form/BaseInput'
import BaseSelect from 'theme/components/core/blocks/Form/BaseSelect'
import ButtonFull from 'theme/components/theme/ButtonFull'
import Tooltip from 'theme/components/core/Tooltip'
import vSelect from 'vue-select'
import format from 'date-fns/format'

export default {
  data () {
    return {
      isClickCollectActive: false,
      isDeliveryTypeActive: false,
      isDateSectionActive: false,
      isDateSelected: false,
      isTimeSelected: false,
      defaultVal: 'Select a Depot',
      depots: [
        'Batleys Aberdeen',
        'Bestway Aintree Liverpool',
        'Batleys Cardiff',
        'Batleys Nottingham',
        'Batleys Gillingham',
        'Bestway Lewisham',
        'Bestway Luton',
        'Batleys Southampton',
        'Bestway Swansea',
        'Bestway Tottenham',
        'Bestway Walsall'
      ],
      times: [
        '10:30',
        '12:30',
        '14:00',
        '17:00'
      ],
      dates: [],
      selectedDate: {},
      selectedTime: {}
    }
  },
  components: {
    ButtonFull,
    Tooltip,
    BaseCheckbox,
    BaseInput,
    BaseSelect,
    vSelect
  },
  mixins: [Shipping],
  methods: {
    changeLabel () {
      this.isDeliveryTypeActive = true
      this.currentDate = new Date()
      for (let i = 0; i < 7; i++) {
        var date = new Date()
        date.setDate(date.getDate() + i + 1)
        this.dates[i] = format(date, 'DD MMMM')
      }
    },
    dateSelected (selectedDate) {
      this.selectedDate = selectedDate
      this.isDateSelected = true
    },
    timeSelected (selectedTime) {
      this.selectedTime = selectedTime
      this.isTimeSelected = true
    }
  },
  computed: {
    countryOptions () {
      return this.countries.map((item) => {
        return {
          value: item.code,
          label: item.name
        }
      })
    }
  },
  validations: {
    shipping: {
      firstName: {
        required,
        minLength: minLength(2)
      },
      lastName: {
        required
      },
      country: {
        required
      },
      streetAddress: {
        required
      },
      apartmentNumber: {
        required
      },
      shippingMethod: {
        required
      },
      zipCode: {
        required,
        minLength: minLength(3)
      },
      city: {
        required
      }
    }
  }
}
</script>

<style lang="scss" scoped>
  .delivery-address-form {
    display: none;
  }
  .delivery-types {
    padding: 30px;
    width: 100%;
    font-size: 20px;
    transition: all 0.2s ease-in;
    &.active{
      background-color: #00a665;
      color: #FFF;
    }
  }
  .v-select .dropdown-toggle {
    width: 200px;
  }
  .dropdown-menu {
    width: 100%;
  }
  .date-btn,
  .time-btn {
    padding: 20px;
    margin: 10px;
    &.active {
      background-color: #0093d8;
      color: #FFF;
    }
  }
</style>
