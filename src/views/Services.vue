<template>
  <div class="flex">
    <v-container fluid>
      <v-row>
        <v-col cols="12">
          <service-filters
            :networkCount="networkCount"
            @clearSearchFilter="clearSearchFilter"
            @openNavDrawer="openNavDrawer"
          />
        </v-col>
      </v-row>
    </v-container>

    <v-navigation-drawer v-model="drawer" absolute temporary width="500">
      <search-bar @closeSearch="drawer = !drawer" />
    </v-navigation-drawer>

    <v-navigation-drawer
      v-model="netselectiondrawer"
      absolute
      temporary
      width="500"
    >
      <netselection-bar
        v-if="netselectiondrawer"
        @closeSearch="netselectiondrawer = !netselectiondrawer"
        @changeBadgeVal="toggleBatchValue"
      />
    </v-navigation-drawer>

    <v-container fluid class="pt-0">
      <v-row no-gutters class="fill-height">
        <v-col cols="4" class="fill-height">
          <service-tabs class="mr-2" />
        </v-col>
        <v-col cols="8">
          <service-detail-tabs class="ml-2" :search_input="searchInput" />
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>
<script>
import ServiceTabs from "../components/service/ServiceTabs";
import ServiceDetailTabs from "../components/service/detail/ServiceDetailTabs";
import SearchBar from "../components/service/search/SearchBar";
import NetselectionBar from "../components/service/search/NetselectionBar";
import { mapState } from "vuex";
import urlSearchParams from "../components/mixins/urlSearchParams";
import ServiceFilters from "../components/service/ServiceFilters.vue";

export default {
  mixins: [urlSearchParams],
  components: {
    SearchBar,
    ServiceTabs,
    ServiceDetailTabs,
    NetselectionBar,
    ServiceFilters,
  },
  data: () => ({
    pnl_search: 0,
    drawer: false,
    netselectiondrawer: false,
    admins: {},
    containerheight: 500,
    showsearchbar: true,
    networkCount: 0,
  }),
  computed: {
    ...mapState("services", ["searchInput", "serviceTabNumber"]),
  },
  created() {
    if (this.$route.hash == "") {
      this.$store.commit("services/UPDATE_SERVICE_TAB_NUMBER", 1);
    } else {
      let filters = this.getFiltersFromUrl(
        this.$store.getters["services/getsearchInputPlain"],
        true
      );

      this.networkCount = filters.search_networks.length;
      this.$store.commit("services/UPDATE_SEARCH_FROM_URLHASH", filters);
      this.$store.commit("services/UPDATE_SERVICE_TAB_NUMBER", 3);
    }
  },
  methods: {
    toggleBatchValue(val) {
      this.networkCount = val.length;
    },
    openNavDrawer(type) {
      if (type == "search") {
        this.drawer = !this.drawer;
      } else {
        this.netselectiondrawer = !this.netselectiondrawer;
      }
    },

    clearSearchFilter() {
      this.$store.commit("services/SET_LOADING_CIRCLE", true);
      this.$store.commit("services/UPDATE_SERVICE_TAB_NUMBER", 1);
      this.$store.commit("services/RESET_SEARCH_STATE");
      this.$store.dispatch("services/getServicesList");
      this.networkCount = 0;
      window.location.hash = "";
    },
  },
};
</script>
