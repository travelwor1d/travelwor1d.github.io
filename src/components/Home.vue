<template>
  <v-container class="selectItems">
    <h3>Vue Select</h3>
    <v-row>
      <!-- 1.Countries -->
      <v-col cols="12" sm="6">
        Countries
        <v-select
          :components="{Deselect, OpenIndicator}"
          :options="countries"
          :searchable="isMobile ? false : true"
          v-model="countrySelected"
          label="name"
          class="selectItems__select"
          placeholder="(type or select)"
        >
          <template v-slot:option="country">
            {{ country.name }}
          </template>
        </v-select>
      </v-col>
      <!-- 2.Years -->
      <v-col cols="12" sm="6">
        Years
        <v-select
          :components="{Deselect, OpenIndicator}"
          :options="years"
          :disabled="countrySelected ? false : true"
          :searchable="isMobile ? false : true"
          class="selectItems__select"
          placeholder="(type or select)"
        >
        </v-select>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import Indicator from './Indicator';
import axios from 'axios';
import { isMobile } from 'mobile-device-detect';

export default {
  name: "Home",
  data() {
    return {
      Deselect: {
        render: createElement => createElement('span', ''),
      },
      OpenIndicator: Indicator,
      countries: [],
      countrySelected: "",
      years: [],
      isMobile: isMobile
    };
  },
  
  created() {
    axios.get('./countries.json')
    .then((res) => {
      this.countries = res.data;
    })
    .catch(error => {
      console.log(error);
    });
  }, 

  watch: {
    countrySelected: function (val, oldVal) {
      console.log(val, oldVal);
      // axios.post('./year.json', {variableName: val})
      axios.get('./year.json')
        .then((res) => {
          this.years = res.data;
        })
        .catch(error => {
          console.log(error);
        })
    },
  }
};
</script>

<style>
  h3 {
    text-align: center;
  }
  .selectItems > div {
    display: flex;
    justify-content: space-around;
    flex-direction: row;
  }

  .vs__selected {
    font-weight: 900;
  }

  .selectItems__select.vs--open .vs__dropdown-toggle {
    box-shadow: 0 0 8px rgb(102 175 233 / 60%);
    border-bottom: none;
    z-index: 21;
    border-color: #66afe9;
    outline: 0;
  }

  .vs__dropdown-toggle {
    border-radius: 0;
  }
</style>
