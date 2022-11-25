<template>
  <div class="main">
    <v-row justify="center" align="center">
      <v-col cols="12" sm="8" md="6">
        <v-card>
          <v-row>
            <v-col class="d-flex" cols="12" sm="5">
              <v-select
                item-text="currencyCode"
                item-value="currencyCode"
                v-model="firstSelected.currencyCode"
                :items="currency"
                outlined
                @change="setFirstCurrency"
              ></v-select>
            </v-col>

            <v-col class="d-flex" cols="12" sm="2">
              <v-row align="center" justify="space-around">
                <v-btn @click="swapCurrency" icon>
                  <v-icon>mdi-swap-horizontal</v-icon>
                </v-btn>
              </v-row>
            </v-col>

            <v-col class="d-flex" cols="12" sm="5">
              <v-select
                item-text="currencyCode"
                item-value="currencyCode"
                v-model="secondSelected.currencyCode"
                :items="currency"
                outlined
                @change="setSecondCurrency"
              ></v-select>
            </v-col>
          </v-row>
          <v-card-title
            v-if="secondSelected.currencyCode === 'USD'"
            justify="center"
            align="center"
            class="notify justify-center"
          >
            {{ $t("main.notify") }}
          </v-card-title>
          <v-form>
            <v-container>
              <v-row>
                <v-col cols="12" md="6">
                  <v-text-field
                    v-model="firstValue"
                    :rules="[rules.counter]"
                    :label="this.$t('main.label_firstCurrency')"
                    counter
                    maxlength="10"
                  ></v-text-field>
                </v-col>

                <v-col cols="12" md="6">
                  <v-text-field
                    :label="'' + secondSelectedCurrency"
                    :disabled="true"
                    required
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-form>
        </v-card>
      </v-col>
    </v-row>

    <v-row justify="center" align="center">
      <currencyList
        :baseCurrent="firstSelected"
        :currency="currency"
        @changeFirstSelect="changeFirstSelect"
      />
    </v-row>
  </div>
</template>

<script>
export default {
  data: () => {
    return {
      firstValue: 1,
      rules: {
        counter: (value) => value.length <= 10 || "Max 10 characters",
      },
      firstSelected: {
        currencyCode: "RUB",
        currencyValue: 1,
      },
      secondSelected: {
        currencyCode: "EUR",
        currencyValue: 0,
      },
      currency: [
        {
          currencyCode: "RUB",
          currencyValue: 1,
        },
      ],
    };
  },

  mounted() {
    this.onLoaded();
  },

  methods: {
    async onLoaded(firstCurrency = this.firstSelected.currencyCode) {
      const currencyLoad = await this.getCurrency(firstCurrency);
      const USDId = currencyLoad.findIndex((el) => el.currencyCode === "BMD");
      currencyLoad[USDId].currencyCode = "USD";
      this.currency = currencyLoad.sort((a, b) =>
        a.currencyCode.localeCompare(b.currencyCode)
      );

      if (firstCurrency === "USD") {
        this.secondSelected = this.setCurrency(currencyLoad, "EUR");
        return;
      }
      this.secondSelected = this.setCurrency(currencyLoad, "USD");
    },

    async getCurrency(firstCurrency) {
      const data = await this.$axios.$get(
        `${process.env.CURRENCY_URL}${process.env.API_KEY}/latest/${firstCurrency}`
      );
      console.log(data, "data");
      const currencyObj = data.conversion_rates;
      let arrCurrency = [];
      for (const currency in currencyObj) {
        let obj = {};
        obj.currencyCode = currency;
        obj.currencyValue = currencyObj[currency];
        arrCurrency.push(obj);
      }
      return arrCurrency;
    },

    async setFirstCurrency(value) {
      this.onLoaded(value);
    },

    setSecondCurrency(value) {
      const findCurrency = this.setCurrency(this.currency, value);
      this.secondSelected = findCurrency;
      this.secondValue = this.secondSelected.currencyValue;
    },

    setCurrency(currencyArr, currency) {
      let CurrencyId = currencyArr.findIndex(
        (el) => el.currencyCode === `${currency}`
      );
      const findCurrency = currencyArr[CurrencyId];
      return findCurrency;
    },

    changeFirstSelect(selected) {
      this.firstSelected = selected;
      this.setFirstCurrency(selected.currencyCode);
    },

    async swapCurrency() {
      const firstCurrency = this.firstSelected;
      const secondCurrency = this.secondSelected;

      const currencyLoad = await this.getCurrency(secondCurrency.currencyCode);
      this.currency = currencyLoad;
      this.firstSelected = secondCurrency;
      this.secondSelected = this.setCurrency(
        currencyLoad,
        firstCurrency.currencyCode
      );
      this.setSecondCurrency(this.secondSelected.currencyCode);
    },
  },

  computed: {
    secondSelectedCurrency() {
      return this.secondValue * this.firstValue;
    },
    secondValue: {
      // getter
      get: function () {
        return this.secondSelected.currencyValue;
      },
      // setter
      set: function (newValue) {
        return this.secondSelected.currencyValue;
      },
    },
  },
};
</script>

<style>
.notify {
  font-size: 12px;
}
.v-text-field__details {
  display: none;
}
.v-sheet.v-card {
  padding: 20px;
}
</style>
