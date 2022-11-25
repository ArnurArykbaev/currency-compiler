<template>
  <v-col cols="12" sm="8" md="6">
    <v-card>
      <v-card-title class="headline justify-center">
        {{ $t("currency_list.title") }}

        <v-col class="d-flex" cols="12" sm="5">
          <v-select
            v-model="baseCurrent.currencyCode"
            item-text="currencyCode"
            item-value="currencyCode"
            :label="baseCurrent.currencyCode"
            :items="currency"
            @input="setBaseCurrency"
            outlined
          ></v-select>
        </v-col>
      </v-card-title>

      <div class="justify-center">
        <v-list class="currency-info justify-center overflow-y-auto" flat>
          <v-list-item-group color="indigo">
            <v-list-item v-for="(item, i) in currency" :key="i">
              <v-row>
                <v-col cols="12" sm="8" md="6">
                  <v-list-item-content
                    class="d-flex justify-center text-center"
                  >
                    <v-list-item-title
                      v-text="item.currencyCode"
                    ></v-list-item-title>
                  </v-list-item-content>
                </v-col>
                <v-col cols="12" sm="8" md="6">
                  <v-list-item-content
                    class="d-flex justify-center text-center"
                  >
                    <v-list-item-title
                      v-text="item.currencyValue"
                    ></v-list-item-title>
                  </v-list-item-content>
                </v-col>
              </v-row>
            </v-list-item>
          </v-list-item-group>
        </v-list>
      </div>
    </v-card>
  </v-col>
</template>

<script>
export default {
  props: {
    baseCurrent: Object,
    currency: Array,
  },
  data: () => {
    return {};
  },
  computed: {},
  watch: {},
  methods: {
    setBaseCurrency(value) {
      const selectedCurrencyId = this.currency.findIndex(
        (el) => el.currencyCode === value
      );
      const selectedCurrency = this.currency[selectedCurrencyId];
      this.$emit("changeFirstSelect", selectedCurrency);
    },
  },
  mounted() {},
};
</script>
<style>
.currency-info {
  max-height: 500px;
}
</style>