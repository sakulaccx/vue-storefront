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
                text: $t('Name must have at least 3 letters.')
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
            :validation="{
              condition: $v.shipping.lastName.$error && !$v.shipping.lastName.required,
              text: $t('Field is required')
            }"
          />

          <base-input
            class="col-xs-12 mb25"
            type="text"
            name="street-address"
            :placeholder="$t('Street name *')"
            v-model.trim="shipping.streetAddress"
            @blur="$v.shipping.streetAddress.$touch()"
            autocomplete="address-line1"
            :validation="{
              condition: $v.shipping.streetAddress.$error && !$v.shipping.streetAddress.required,
              text: $t('Field is required')
            }"
          />

          <base-input
            class="col-xs-12 mb25"
            type="text"
            name="apartment-number"
            :placeholder="$t('House/Apartment number *')"
            v-model.trim="shipping.apartmentNumber"
            @blur="$v.shipping.apartmentNumber.$touch()"
            autocomplete="address-line2"
            :validation="{
              condition: $v.shipping.apartmentNumber.$error && !$v.shipping.apartmentNumber.required,
              text: $t('Field is required')
            }"
          />

          <base-input
            class="col-xs-12 col-sm-6 mb25"
            type="text"
            name="city"
            :placeholder="$t('City *')"
            v-model.trim="shipping.city"
            @blur="$v.shipping.city.$touch()"
            autocomplete="city"
            :validation="{
              condition: $v.shipping.city.$error && !$v.shipping.city.required,
              text: $t('Field is required')
            }"
          />

          <base-input
            class="col-xs-12 col-sm-6 mb25"
            type="text"
            name="state"
            :placeholder="$t('State / Province')"
            v-model.trim="shipping.state"
            autocomplete="state"
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

          <div class="col-xs-12 col-sm-6 mb25">
            <select
              name="countries"
              :class="{'cl-tertiary' : shipping.country.length === 0}"
              v-model="shipping.country"
              @change="$v.shipping.country.$touch(); changeCountry();"
              autocomplete="country"
            >
              <option value="" disabled selected hidden>{{ $t('Country') }} *</option>
              <option v-for="country in countries" :key="country.code" :value="country.code">{{ country.name }}</option>
            </select>
            <span class="validation-error" v-if="$v.shipping.country.$error && !$v.shipping.country.required">
              {{ $t('Field is required') }}
            </span>
          </div>

          <base-input
            class="col-xs-12 mb25"
            type="text"
            name="phone-number"
            :placeholder="$t('Phone Number')"
            v-model.trim="shipping.phoneNumber"
            autocomplete="phone-number"
          />

          <h4 class="col-xs-12">
            {{ $t('Shipping method') }}
          </h4>
          <div v-for="(method, index) in shippingMethods" :key="index" class="col-md-6">
            <label class="radioStyled"> {{ method.method_title }} | {{ method.amount | price }}
              <input
                type="radio"
                :value="method.method_code"
                name="shipping-method"
                v-model="shipping.shippingMethod"
                @change="$v.shipping.shippingMethod.$touch(); changeShippingMethod();"
              >
              <span class="checkmark"/>
            </label>
          </div>
          <span class="validation-error" v-if="$v.shipping.shippingMethod.$error && !$v.shipping.shippingMethod.required">
            {{ $t('Field is required') }}
          </span>
        </div>
      </div>
    </div>
    <div class="row" v-if="isActive">
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-12 col-sm-9 col-md-11">
        <div class="row">
          <div class="col-xs-12 col-md-8 my30 px20">
            <button-full
              @click.native="sendDataToCheckout"
              :class="{ 'ripple': true, 'button-disabled' : $v.shipping.$invalid}"
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
          <div class="col-xs-12 h4">
            <p>
              {{ shipping.firstName }} {{ shipping.lastName }}
            </p>
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
            <div class="col-xs-12">
              <h4>
                {{ $t('Shipping method') }}
              </h4>
            </div>
            <div class="col-md-6 mb15">
              <label class="radioStyled"> {{ getShippingMethod().method_title }} | {{ getShippingMethod().amount | price }}
                <input type="radio" value="" checked disabled name="chosen-shipping-method">
                <span class="checkmark"/>
              </label>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { coreComponent } from 'core/lib/themes'
import ButtonFull from 'theme/components/theme/ButtonFull.vue'
import Tooltip from 'theme/components/core/Tooltip.vue'
import BaseCheckbox from '../Form/BaseCheckbox.vue'
import BaseInput from '../Form/BaseInput.vue'
import { required, minLength } from 'vuelidate/lib/validators'

// https://monterail.github.io/vuelidate/#sub-contextified-validators

export default {
  validations: {
    shipping: {
      firstName: {
        required,
        minLength: minLength(3)
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
  },
  components: {
    ButtonFull,
    Tooltip,
    BaseCheckbox,
    BaseInput
  },
  mixins: [coreComponent('blocks/Checkout/Shipping')]
}
</script>
