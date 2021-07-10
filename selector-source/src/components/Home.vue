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
          :calculate-position="withPopper"
          append-to-body 
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
          :calculate-position="withPopper"
          append-to-body 
          ref="yearSelect"
          class="selectItems__select"
          placeholder="(type or select)"
        >
        </v-select>
      </v-col>
    </v-row>
    <v-row class="bottom-class">
      <!-- 3.Countries -->
      <v-col cols="12">
        Countries (Default: United State)
        <v-select
          :components="{Deselect, OpenIndicator}"
          :options="countries"
          :searchable="isMobile ? false : true"
          :calculate-position="withPopper"
          append-to-body 
          v-model="defaultCountry"
          label="name"
          class="selectItems__select"
          placeholder="(type or select)"
        >
          <template v-slot:option="country">
            {{ country.name }}
          </template>
        </v-select>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import Indicator from './Indicator';
import axios from 'axios';
import { isMobile } from 'mobile-device-detect';
import { createPopper } from '@popperjs/core';

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
      defaultCountry: "",
      years: [],
      isMobile: isMobile,
      placement: "bottom"
    };
  },
  
  created() {
    axios.get('./countries.json')
    .then((res) => {
      this.countries = res.data;
      this.defaultCountry = "United States"
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
          // data for referencing selector
          this.years = res.data;

          // trigger
          this.$refs["yearSelect"].open = true;
          this.$refs.yearSelect.$refs.search.focus();
        })
        .catch(error => {
          console.log(error);
        })
    },
  },

  methods: {
    withPopper (dropdownList, component, {width}) {
      /**
       * We need to explicitly define the dropdown width since
       * it is usually inherited from the parent with CSS.
       */
      dropdownList.style.width = width;

      /**
       * Here we position the dropdownList relative to the $refs.toggle Element.
       *
       * The 'offset' modifier aligns the dropdown so that the $refs.toggle and
       * the dropdownList overlap by 1 pixel.
       *
       * The 'toggleClass' modifier adds a 'drop-up' class to the Vue Select
       * wrapper so that we can set some styles for when the dropdown is placed
       * above.
       */
      const popper = createPopper(component.$refs.toggle, dropdownList, {
        placement: this.placement,
        modifiers: [
          {
            name: 'offset', options: {
              offset: [0, -1]
            }
          },
          {
            name: 'toggleClass',
            enabled: true,
            phase: 'write',
            fn ({state}) {
              component.$el.classList.toggle('drop-up', state.placement === 'top')
            },
          }]
      });

      /**
       * To prevent memory leaks Popper needs to be destroyed.
       * If you return function, it will be called just before dropdown is removed from DOM.
       */
      return () => popper.destroy();
    }
  }
};
</script>

<style>
  h3 {
    text-align: center;
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

  .bottom-class {
    position: absolute;
    bottom: 50px;
    width: 100%;
  }

</style>
