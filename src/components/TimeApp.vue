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
      time: '20:00',
      time_computer_language: '20:00',
    };
  },
  mounted() {
    this.areas = ['Africa/Abidjan', 'Africa/Accra', 'Africa/Algiers', 'Africa/Bissau', 'Africa/Cairo'];
    this.locations = ['Europe/Amsterdam', 'Europe/Andorra', 'Europe/Astrakhan',
      'Europe/Athens', 'Europe/Belgrade', 'Europe/Berlin', 'Europe/Brussels', 'Europe/Bucharest',
      'Europe/Budapest', 'Europe/Chisinau', 'Europe/Copenhagen', 'Europe/Dublin', 'Europe/Gibraltar',
      'Europe/Helsinki', 'Europe/Istanbul'];
  },
  onChangeArea() {
    console.log('Area Changed');
  },
  onChangeLocation() {
    console.log('location Changed');
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

a {
  color: #42b983;
}
</style>
