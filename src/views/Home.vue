<template>
  <main v-if="!loading">
    <DataTitle :text="title" :dataDate="dataDate" />

    <DataBoxes :stats="stats" />

    <CountrySelect
      @get-country="getCountryData"
      @get-global="clearCountryData"
      :countries="countries"
    />

    <div class="flex flex-col justify-center text-center align-bottom">
      <p
        class="
          text-gray-400 text-sm
          my-10
          hover:text-gray-500
          duration-150
        "
      >
        made by linus
      </p>
    </div>
  </main>

  <main class="flex flex-col align-center justify-center text-center" v-else>
    <div class="text-gray-500 text-3xl mt-10 mb-6">Fetching Data</div>
    <img :src="loadingImage" class="w-24 m-auto" alt="Covid Tracker Logo" />
  </main>
</template>

<script>
import DataTitle from "@/components/DataTitle";
import DataBoxes from "@/components/DataBoxes";
import CountrySelect from "@/components/CountrySelect";

export default {
  name: "Home",
  components: {
    DataTitle,
    DataBoxes,
    CountrySelect,
  },
  data() {
    return {
      loading: true,
      title: "Global",
      dataDate: "",
      status: {},
      countries: [],
      loadingImage: require("../assets/hourglass.gif"),
    };
  },
  methods: {
    async fetchCovidData() {
      const res = await fetch("https://api.covid19api.com/summary");
      const data = await res.json();
      return data;
    },
    async getCountryData(country) {
      const data = await this.fetchCovidData();
      this.stats = country;
      this.title = country.Country;
    },
    async clearCountryData() {
      const data = await this.fetchCovidData();
      this.title = "Global";
      this.stats = data.Global;
    },
  },
  async created() {
    const data = await this.fetchCovidData();
    this.dataDate = data.Date;
    this.stats = data.Global;
    this.countries = data.Countries;
    this.loading = false;
  },
};
</script>