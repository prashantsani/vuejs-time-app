<template>
  <div class="hello">
      <h1>Time App in VueJS</h1>
      <fieldset class="my-3">
        <label for="user-select-area" class="mr-2">Area</label>
          <select v-model="selectedArea" @change="onChangeArea"
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
            <option selected disabled value="">Please select Location</option>
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
      allLocations: [],
      temp1: new Map(),
      tempMAP: [],
      areas: [],
      locations: [],
      selectedArea: 0,
      selectedLocation: 0,
      time: 'Please Select Area',
      time_computer_language: '',
      date: 'Please Select Area',
      week: ['SUN', 'MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT'],
    };
  },
  mounted() {
    fetch('http://worldtimeapi.org/api/timezone')
      .then((res) => res.json())
      .then((response) => {
        this.allLocations = response;

        this.tempMAP = this.allLocations.map((item) => {
          let area = item.substring(0, item.indexOf('/'));
          let location = item.substring(item.indexOf('/') + 1, item.length);

          if (area === '') { area = item; location = item; }

          if (!this.temp1.has(area)) {
            this.temp1.set(area, []);
          }

          this.temp1.get(area).push(location);
          // console.log(this.temp1.get(area));

          return false;
        });

        window.abc = this.temp1;
        this.areas = this.temp1.keys();
      });
  },
  methods: {
    zeroPadding(num, digit) {
      let zero = '';
      for (let i = 0; i < digit; i += 1) {
        zero += '0';
      }
      return (zero + num).slice(-digit);
    },
    onChangeArea(e) {
      const selectedArea = e.target.value;
      this.locations = [];
      this.selectedArea = selectedArea;
      this.time = 'Loading';
      this.time_computer_language = '';


      fetch(`http://worldtimeapi.org/api/timezone/${selectedArea}`)
        .then((res) => res.json())
        .then((response) => {
          const time = new Date(response.utc_datetime).toLocaleString('en-US', { timeZone: this.selectedArea });

          this.date = time.substring(0, time.indexOf(', '));
          this.time = time.substr(time.indexOf(', ')).replace(', ', '');

          // Ideally it should be sent as a Date() object,
          // But unfortunately Date() converts to LOCAL Date() object

          // eslint-disable-next-line max-len
          // this.time = `${zeroPadding(cd.getHours(), 2)} : ${zeroPadding(cd.getMinutes(), 2)} : ${zeroPadding(cd.getSeconds(), 2)}`;
          // eslint-disable-next-line max-len
          // this.date = `${zeroPadding(cd.getFullYear(), 4)} - ${zeroPadding(cd.getMonth() + 1, 2)} - ${zeroPadding(cd.getDate(), 2)} ${this.week[cd.getDay()]}`;
        });
    },
    onChangeLocation() {
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
