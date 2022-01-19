<template>
  <div>
      <h1 class="text-center">🌍 World Clock ⏱</h1>
      <form @submit.prevent class="select-time"><!-- Form submit will not reload the page -->
        <fieldset class="my-3">
          <label for="user-select-area" class="mr-2">Area</label>
            <select @change.prevent="onChangeArea"
                name="user-select-area" id="user-select-area" tabindex="1">
                <option selected disabled value="">{{this.areaPlaceholder}}</option>
                <option v-for="area in areas" :key="area" v-bind:value="area">
                  {{area}}
                </option>
            </select>
        </fieldset>
        <fieldset class="my-3">
          <label for="user-select-area" class="mr-2">Location</label>
          <select v-model="selectedLocation" @change.prevent="onChangeLocation"
              name="user-select-location" id="user-select-location" tabindex="2">
              <option selected disabled value="selectLocation">{{this.locationPlaceholder}}</option>
              <option v-for="location in locations" :key="location" v-bind:value="location">
                {{location}}
              </option>
          </select>
        </fieldset>
      </form>
      <Clock :date="date" :time="time" />
  </div>
</template>

<script>
import Clock from './Clock.vue';

const axios = require('axios');
const dayjs = require('dayjs');


function handleErrors(error) {
  let timeStr;
  let dateStr;

  if (error.toString().indexOf('Network Error')) {
    // We can set a timer here and call again
    dateStr = 'Please refresh or try again later';
    timeStr = 'Network Error!';
  } else {
    dateStr = 'Please refresh or try again later';
    timeStr = 'Error!';
  }

  return {
    date: dateStr,
    time: timeStr,
  };
}

export default {
  name: 'TimeApp',
  data() {
    return {
      areaPlaceholder: 'Loading', // This will be shown till all areas/locations are loaded
      locationPlaceholder: '', // Once all locations are loaded, 'Please Select Area first'
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
      api: 'https://worldtimeapi.org/api/timezone',
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
          this.areaPlaceholder = 'Select Area';
          this.locationPlaceholder = 'Select Location';

          return false;
        });
        this.setAreas(Array.from(this.areaLocationMap.keys()));
      })
      .catch((error) => {
        const msg = handleErrors(error);

        this.time = msg.time;
        this.date = msg.date;
        this.areaPlaceholder = msg.time;
      });
  },
  methods: {
    getAreas() {
      return this.areas;
    },
    setAreas(value) {
      this.areas = value;
    },
    onChangeArea(e) {
      const selectedArea = e.target.value;
      this.locations = Array.from(this.areaLocationMap.get(selectedArea));
      this.selectedArea = selectedArea;
    },
    onChangeLocation(e) {
      this.time = 'Loading';
      this.date = 'Loading';
      if ((e.target.value !== '') && (e.target.value !== 'selectLocation')) {
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
            const dt = new Date(response.data.utc_datetime).toLocaleString('en-US', { timeZone: this.timeZone });
            this.date = dayjs(dt).format('DD/MM/YYYY');
            this.time = dayjs(dt).format('HH:mm:ss');
            console.log(response.data);
          })
          .catch((error) => {
            const msg = handleErrors(error);

            this.time = msg.time;
            this.date = msg.date;
          });
      }
    },
  },
  components: {
    Clock,
  },
};
</script>

<style lang="scss" scoped>
.select-time{
  width: 550px;
  margin: 0 auto;
  max-width: 80%;

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

@media screen and (max-height: 700px) {
  .select-time{
    select{
      height: 6vh;
    }
  }
}

@media screen and (max-width: 770px) {
  .select-time{
    max-width: 80%;
    margin-left: auto;
    margin-right: auto;
    label{
      display: none;
    }
    select{
      width: 100%;
      margin-left: 0;
    }
  }
}
</style>
