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
      <p class="text-success h4">
        <time v-bind:datetime="time_computer_language">{{time}}</time>
      </p>
  </div>
</template>

<script>
export default {
  name: 'TimeApp',
  data() {
    return {
      areas: [],
      locations: [],
      selectedArea: 0,
      selectedLocation: 0,
      time: 'Please Select Area',
      time_computer_language: '',
    };
  },
  mounted() {
    fetch('http://worldtimeapi.org/api/timezone')
      .then((res) => res.json())
      .then((response) => { this.areas = response; });
  },
  methods: {
    onChangeArea(e) {
      const selectedArea = e.target.value;
      this.locations = [];
      this.selectedArea = selectedArea;
      this.time = 'Loading';
      this.time_computer_language = '';
      console.log(selectedArea);


      fetch(`http://worldtimeapi.org/api/timezone/${selectedArea}`)
        .then((res) => res.json())
        .then((response) => {
          const regex = /T|\\:\d\dZ/g;
          const dateString = response.utc_datetime.replace(regex, ' ');
          this.time = dateString;
          this.time_computer_language = response.utc_datetime;
        });
    },
    onChangeLocation(e) {
      console.log(e.target.value);
    },
  },
};
</script>

<style scoped>

</style>
