<template>
  <div id="app">
    <Nevbar />
    <br />
    <div class="row">
      <div class="col s12 m8 l9">
        <select v-on:change="getCountry">
          <option value="worldwide">world wide</option>
          <option
            v-bind:value="country.value"
            v-bind:key="country.id"
            v-for="country in countries"
          >{{country.name}}</option>
        </select>
        <div class="row">
          <Card v-bind:info="countryInfo" />
          <Recorverd v-bind:info="countryInfo" />
          <Deaths v-bind:info="countryInfo" />
        </div>
        <MapImage />
      </div>

      <div class="col s12 m4 l3">
        <h5 class="center">Live cases</h5>
        <table class="striped">
          <tbody>
            <tr v-bind:key="data.name" v-for="data in tableData">
              <td class="center center-align">{{data.name}}</td>
              <td class="center center-align">{{data.cases}}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>


<script>
import "materialize-css/dist/css/materialize.min.css";
import Nevbar from "./Nevbar.vue";
import Card from "./Card.vue";
import Recorverd from "./Recorverd.vue";
import Deaths from "./Deaths.vue";
import MapImage from "./MapImage.vue";

import M from "materialize-css";

export default {
  name: "App",
  components: {
    Nevbar,
    Card,
    Recorverd,
    Deaths,
    MapImage
  },
  methods: {
    async getCountry(e) {
      if (e.target.value === "worldwide") {
        const worldWide = await fetch("https://www.disease.sh/v3/covid-19/all");
        const wolrdWideResult = await worldWide.json();
        this.countryInfo = wolrdWideResult;
      } else {
        const countryCode = e.target.value;
        const country = await fetch(
          `https://disease.sh/v3/covid-19/countries/${countryCode}`
        );
        const countryResult = await country.json();
        this.countryInfo = countryResult;
      }
    }
  },
  mounted() {
    const elems = document.querySelectorAll("select");
    M.FormSelect.init(elems, {});
  },
  async created() {
    const apiCall = await fetch("https://www.disease.sh/v3/covid-19/countries");
    const result = await apiCall.json();

    const worldWide = await fetch("https://www.disease.sh/v3/covid-19/all");
    const wolrdWideResult = await worldWide.json();

    const tableInfo = result.map(country => {
      return {
        name: country.country,
        cases: country.cases
      };
    });

    const countries = result.map(country => {
      return {
        name: country.country,
        id: country.countryInfo._id,
        value: country.countryInfo.iso2,
        cases: country.cases
      };
    });
    this.countries = countries;
    this.countryInfo = wolrdWideResult;
    this.tableData = tableInfo
      .sort((a, b) => {
        if (a.cases > b.cases) {
          return -1;
        } else {
          return 1;
        }
      })
      .slice(0, 17);

    const elems = await document.querySelectorAll("select");
    await M.FormSelect.init(elems, {});
  },
  data() {
    return {
      countries: [],
      country: "worldwide",
      countryInfo: {},
      tableData: []
    };
  }
};
</script>

<style>
</style>
