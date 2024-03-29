<template>
  <page-component class="rates-widget">
    <template v-slot:head-left>Rates</template>
    <template v-slot:head-right>
      <dropdown
         :items="fiats"
         v-model="selectedCurrency"
      />
      <!-- <div class="rates-settings-btn">
        <svg viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path
            d="M17.14 10.9361C17.176 10.6361 17.2 10.3241 17.2 10.0001C17.2 9.67605 17.176 9.36406 17.128 9.06406L19.156 7.48005C19.243 7.40717 19.3024 7.30665 19.3242 7.19527C19.3461 7.0839 19.329 6.9684 19.276 6.86805L17.356 3.54406C17.236 3.32806 16.984 3.25606 16.768 3.32806L14.38 4.28806C13.8841 3.90527 13.3393 3.59048 12.76 3.35206L12.4 0.808055C12.3826 0.693598 12.3245 0.589269 12.2363 0.514293C12.1481 0.439318 12.0358 0.398746 11.92 0.400055H8.07999C7.96554 0.398894 7.85465 0.439805 7.76837 0.515019C7.6821 0.590234 7.62645 0.694513 7.61199 0.808055L7.25199 3.35206C6.67368 3.59246 6.1291 3.9071 5.63199 4.28806L3.24399 3.32806C3.13646 3.2866 3.01752 3.28565 2.90934 3.32539C2.80116 3.36513 2.71112 3.44285 2.65599 3.54406L0.735992 6.86805C0.676613 6.9668 0.656012 7.08408 0.678183 7.19715C0.700355 7.31023 0.763719 7.41104 0.855992 7.48005L2.88399 9.06406C2.83599 9.36406 2.79999 9.68806 2.79999 10.0001C2.79999 10.3121 2.82399 10.6361 2.87199 10.9361L0.843992 12.5201C0.756988 12.5929 0.697612 12.6935 0.675773 12.8048C0.653934 12.9162 0.670954 13.0317 0.723992 13.1321L2.64399 16.4561C2.76399 16.6721 3.01599 16.7441 3.23199 16.6721L5.61999 15.7121C6.11588 16.0948 6.6607 16.4096 7.23999 16.6481L7.59999 19.1921C7.64799 19.4321 7.83999 19.6001 8.07999 19.6001H11.92C12.16 19.6001 12.364 19.4321 12.388 19.1921L12.748 16.6481C13.3263 16.4076 13.8709 16.093 14.368 15.7121L16.756 16.6721C16.972 16.7561 17.224 16.6721 17.344 16.4561L19.264 13.1321C19.3234 13.0333 19.344 12.916 19.3218 12.803C19.2996 12.6899 19.2363 12.5891 19.144 12.5201L17.14 10.9361ZM9.99999 13.6001C8.01999 13.6001 6.39999 11.9801 6.39999 10.0001C6.39999 8.02006 8.01999 6.40005 9.99999 6.40005C11.98 6.40005 13.6 8.02006 13.6 10.0001C13.6 11.9801 11.98 13.6001 9.99999 13.6001Z"
            fill="#2DCCD3"
          />
        </svg>
      </div> -->
    </template>
    <template v-slot:default>
      <table class="rates-list">
        <tbody>
          <tr v-for="rate in rates" class="rate" :key="rate.name">
            <td>
              <div class="rate__icon">
                <img :src="`/img/wallets/${rate.name}.svg`" :alt="rate.name" />
              </div>
            </td>
            <td class="rate__name">{{ rate.text | capitalize }}</td>
            <td class="rate__value">{{ selectedCurrency.symbol }} {{ NumberWithCommas(rate.currentRate) }}</td>
            <td
              class="rate__status"
              :class="{'up': calculateChangingRate(rate.name) > 0, 'down': calculateChangingRate(rate.name) < 0}"
            > {{ calculateChangingRate(rate.name) || 0 }}%</td>
          </tr>
        </tbody>
      </table>
    </template>
  </page-component>
</template>

<script>
import BigNumber from 'bignumber.js';
import financeFuncs from '~/mixins/finance';
import Dropdown from '../dropdown/index.vue';

export default {
  name: 'rates-widget',
  components: { Dropdown },
  data: () => ({
    currencies: [
      {
        text: 'HDP.ф', name: 'hdp.ф', symbol: 'Hdp.ф', isFiat: false,
      },
      {
        text: 'ETH', name: 'eth', symbol: 'ETH', isFiat: false,
      },
      {
        text: 'BTC', name: 'btc', symbol: 'BTC', isFiat: false,
      },
      {
        text: 'AED', name: 'aed', symbol: 'Dh', isFiat: true,
      },
      {
        text: 'GBP', name: 'gbp', symbol: '£', isFiat: true,
      },
      {
        text: 'USD', name: 'usd', symbol: '$', isFiat: true,
      },
      {
        text: 'EUR', name: 'eur', symbol: '€', isFiat: true,
      },
      {
        text: 'CAD', name: 'cad', symbol: 'CA$', isFiat: true,
      },
    ],
    selectedCurrency: { name: 'usd', symbol: '$' },
  }),
  mixins: [financeFuncs],
  computed: {
    rates() {
      const rates = [];
      if (this.currencies) {
        this.currencies.forEach((currency) => {
          if (currency.name !== this.selectedCurrency.name) {
            rates.push({
              currentRate: this.financeConvert(
                currency.name,
                this.selectedCurrency.name,
              ),
              ...currency,
            });
          }
        });
      }
      return rates;
    },
    fiats() {
      return this.currencies.filter((item) => item.isFiat);
    },
  },
  methods: {
    getChangingRate(name) {
      // get current currency change rate
      // get currency change rate of the vowel that's shown
      const rate = this._rates.find((element) => element.id === name);
      if (rate != null) {
        const value = rate.change;
        if (value) {
          return parseFloat(value).toFixed(2);
        }
        return value;
      }
      return 0;
    },
    calculateChangingRate(name) {
      let rate = this.getChangingRate(name);
      const selectedRate = this.getChangingRate(this.selectedCurrency.name);
      rate = new BigNumber(rate);
      return rate.minus(selectedRate).toNumber();
    },
  },
};
</script>

<style lang="scss" scoped>
.rates-settings-btn {
  display: block;
  width: 20px;
  height: 20px;
  cursor: pointer;
}
.rates-list {
  padding: 0 10px;
  width: 100%;
}
@media (max-width: 575px) {
  .rates-list {
    padding: 0;
  }
}
.rate {
  width: 100%;
  &__icon {
    width: 27px;
    height: 27px;
    img {
      display: block;
      width: 100%;
      height: 100%;
    }
  }
  &__name {
    padding: 0 16px 0 28px;
    font-size: 16px;
    line-height: 1.18;
    letter-spacing: 0.06em;
    text-align: left;
    color: $main-dark;
  }
  &__value {
    width: 60%;
    padding: 0 8px;
    font-weight: 500;
    font-size: 20px;
    line-height: 1.15;
    text-align: left;
    letter-spacing: 0.06em;
    color: $main-dark;
  }
  &__status {
    padding: 0 8px;
    font-size: 16px;
    line-height: 1.18;
    letter-spacing: 0.06em;
    text-align: right;
    white-space: nowrap;
    &.up {
      color: $green;
      &::before {
        content: "+";
        margin-right: -5px;
      }
    }
    &.down {
      color: $red;
    }
  }
  td {
    padding-top: 21px;
    padding-bottom: 21px;
    vertical-align: middle;
    white-space: nowrap;
    &:first-child {
      padding-left: 0;
    }
    &:last-child {
      padding-right: 0;
    }
  }
  @media (max-width: 768px) {
    .rate__value {
      font-size: 0.8rem;
    }
    .rate__status {
      font-size: 0.8rem;
    }
  }
}
</style>
