<template>
  <div class="hello">
      <h1 class="text-center">Time App in VueJS</h1>
      <form @submit="() => { return false }" class="selectTime">
        <fieldset class="my-3">
          <label for="user-select-area" class="mr-2">Area</label>
            <select @change="onChangeArea"
                name="user-select-area" id="user-select-area" tabindex="1">
                <option selected disabled value="">Please select Area</option>
                <option v-for="area in areas" :key="area" v-bind:value="area">
                  {{area}}
                </option>
            </select>
        </fieldset>
        <fieldset class="my-3">
          <label for="user-select-area" class="mr-2">Location</label>
          <select v-model="selectedLocation" @change="onChangeLocation"
              name="user-select-location" id="user-select-location" tabindex="2">
              <option selected disabled value="selectLocation">Please select Location</option>
              <option v-for="location in locations" :key="location" v-bind:value="location">
                {{location}}
              </option>
          </select>
        </fieldset>
      </form>
      <div id="clock" class="clock">
          <p class="clock__date">{{ date }}</p>
          <p class="clock__time">{{ time }}</p>
      </div>
  </div>
</template>

<script>
const axios = require('axios');

export default {
  name: 'TimeApp',
  data() {
    return {
      allLocations: [], // Array of strings (timeZones) recieved via API
      areaLocationMap: new Map(), // Using JavaScript's Map() object to map all Areas/Location
      areas: [], // All Areas Available binded to <select>
      locations: [], // All Locations in that area available {Dynamic}; binded to <select>
      selectedArea: null, // {String} Value of Selected Area
      selectedLocation: null, // {String} Value of Selected Location
      time: 'Select Area', // The Time shown inside <p>
      date: 'Please', // The Date shown inside <p>
      week: ['SUN', 'MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT'], // All available Days of Week
      timeZone: '', // Not Local Machine TimeZone
      api: 'http://worldtimeapi.org/api/timezone',
    };
  },
  mounted() {
    axios.get(this.api)
      .then((response) => {
        this.allLocations = response.data;

        this.allLocations.map((item) => {
          let area = item.substring(0, item.indexOf('/'));
          let location = item.substring(item.indexOf('/') + 1, item.length);

          if (area === '') { area = item; location = item; }

          if (!this.areaLocationMap.has(area)) {
            this.areaLocationMap.set(area, []);
          }

          this.areaLocationMap.get(area).push(location);

          return false;
        });

        window.abc = this.areaLocationMap;
        this.setAreas(Array.from(this.areaLocationMap.keys()));
      })
      .catch((error) => {
        console.log(error);
      });
  },
  methods: {
    getAreas() {
      return this.areas;
    },
    setAreas(value) {
      this.areas = value;
    },
    zeroPadding(num, digit) {
      let zero = '';
      for (let i = 0; i < digit; i += 1) {
        zero += '0';
      }
      return (zero + num).slice(-digit);
    },
    onChangeArea(e) {
      const selectedArea = e.target.value;
      this.locations = Array.from(this.areaLocationMap.get(selectedArea));
      this.selectedArea = selectedArea;
    },
    onChangeLocation(e) {
      e.preventDefault();

      this.time = 'Loading';

      if (e.target.value === '' || e.target.value === 'selectLocation') { return false; }

      const location = e.target.value;
      this.selectedLocation = location;
      this.timeZone = (() => {
        if (this.selectedLocation === this.selectedArea) {
          return `${this.selectedArea}`;
        }
        return `${this.selectedArea}/${this.selectedLocation}`;
      })();

      const url = `${this.api}/${this.timeZone}`;

      axios.get(url)
        .then((response) => {
          if (typeof response.data !== 'string') {
            this.time = 'Error ';
          }

          const time = new Date(response.data.utc_datetime).toLocaleString('en-US', { timeZone: this.timeZone });

          this.date = time.substring(0, time.indexOf(', '));
          this.time = time.substr(time.indexOf(', ')).replace(', ', '');
        })
        .catch((error) => {
          console.log(error);
        });

      return false;
    },
  },
};
</script>

<style lang="scss" scoped>
.selectTime{
  width: 550px;
  margin: 0 auto;
  max-width: 100%;

  fieldset{
    border: none;
    outline: none;
    margin: 30px 0
  }
  label,
  select{
    display: inline-block; //Normally I would use a bootstarp/tailwind class here
    font-size: 1.7rem;
  }

  label{
    width: 130px;
    text-align: right;
  }

  select{
    height: 40px;
    margin-left: 30px;
    width: calc(100% - 160px);
  }

}

.clock {
    font-family: 'Share Tech Mono', monospace;
    color: #ffffff;
    text-align: center;
    transform: scale(0.8);
    color: #daf6ff;
    text-shadow: 0 0 20px rgba(10, 175, 230, 1),  0 0 20px rgba(10, 175, 230, 0);
    margin: 10vh auto 0;

    &__time {
      letter-spacing: 0.05em;
      font-size: 5.61rem;
      padding: 5px 0;
    }

    &__date {
      letter-spacing: 0.1em;
      font-size: 1.333rem;
    }
}
</style>
