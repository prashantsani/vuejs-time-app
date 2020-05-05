<template>
  <div class="hello">
      <h1>Time App in VueJS</h1>
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
      <div id="clock">
          <p class="date">{{ date }}</p>
          <p class="time">{{ time }}</p>
      </div>
  </div>
</template>

<script>
export default {
  name: 'TimeApp',
  data() {
    return {
      allLocations: [], // Array of strings (timeZones) recieved via API
      areaLocationMap: new Map(), // Using JavaScript's Map() object to map all Areas/Location
      areas: [], // All Areas Available binded to <select>
      locations: [], // All Locations in that area available {Dynamic}; binded to <select>
      selectedArea: 0, // {String} Value of Selected Area
      selectedLocation: 0, // {String} Value of Selected Location
      time: 'Select Area', // The Time shown inside <p>
      date: 'Please', // The Date shown inside <p>
      week: ['SUN', 'MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT'], // All available Days of Week
      timeZone: '', // Not Local Machine TimeZone
      api: 'http://worldtimeapi.org/api/timezone',
    };
  },
  mounted() {
    console.log('dasjkdas');
    fetch(this.api)
      .then((res) => res.json())
      .then((response) => {
        this.allLocations = response;

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

      fetch(url)
        .then((res) => res.json())
        .then((response) => {
          console.log(response);

          if (typeof response !== 'string') {
            this.time = 'Error ';
          }

          const time = new Date(response.utc_datetime).toLocaleString('en-US', { timeZone: this.timeZone });

          this.date = time.substring(0, time.indexOf(', '));
          this.time = time.substr(time.indexOf(', ')).replace(', ', '');

          // Ideally it should be sent as a Date() object,
          // But unfortunately Date() converts to LOCAL Date() object

          // eslint-disable-next-line max-len
          // this.time = `${zeroPadding(cd.getHours(), 2)} : ${zeroPadding(cd.getMinutes(), 2)} : ${zeroPadding(cd.getSeconds(), 2)}`;
          // eslint-disable-next-line max-len
          // this.date = `${zeroPadding(cd.getFullYear(), 4)} - ${zeroPadding(cd.getMonth() + 1, 2)} - ${zeroPadding(cd.getDate(), 2)} ${this.week[cd.getDay()]}`;
        })
        .catch((error) => {
          console.log(error);
        });

      return false;
    },
  },
};
</script>

<style>
html,body {
    height: 100%;
}
body {
    background: #0f3854;
    background: radial-gradient(ellipse at center,  #0a2e38  0%, #000000 70%);
    background-size: 100%;
}
p {
    margin: 0;
    padding: 0;
}
#clock {
    font-family: 'Share Tech Mono', monospace;
    color: #ffffff;
    text-align: center;
    transform: scale(0.8);
    color: #daf6ff;
    text-shadow: 0 0 20px rgba(10, 175, 230, 1),  0 0 20px rgba(10, 175, 230, 0);
}
#clock .time {
        letter-spacing: 0.05em;
        font-size: 80px;
        padding: 5px 0;
    }
#clock    .date {
        letter-spacing: 0.1em;
        font-size: 24px;
    }
#clock    .text {
        letter-spacing: 0.1em;
        font-size: 12px;
        padding: 20px 0 0;
    }
</style>
