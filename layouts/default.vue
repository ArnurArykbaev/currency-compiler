<template>
  <v-app dark>
    <v-app-bar app>
      <span> {{ $t("main.current_date") }} {{ getTime() }}</span>
      <v-spacer />
      <v-list>
        <v-list-item
          elevation="2"
          href="#"
          v-for="locale in availableLocales"
          :key="locale.code"
          @click.prevent.stop="$i18n.setLocale(locale.code)"
          ><v-btn color="primary" dark>
            {{ locale.name }}
          </v-btn></v-list-item
        >
      </v-list>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>

    <v-footer app> </v-footer>
  </v-app>
</template>

<script>
import moment from "moment/moment";
export default {
  name: "DefaultLayout",
  data() {
    return {
    };
  },
  methods: {
    getTime() {
      return moment(new Date()).format("YYYY.MM.DD  hh:mm");
    },
  },
  computed: {
    availableLocales() {
      return this.$i18n.locales.filter((i) => i.code !== this.$i18n.locale);
    },
  },
};
</script>
