<template>
  <div id="clock" class="clock">
      <p class="clock__date">{{ date }}</p>
      <p class="clock__time">{{ time }}</p>
  </div>
</template>

<script>
export default {
  name: 'Clock',
  props: ['date', 'time'],
};
</script>

<style lang="scss" scoped>
.clock {
    text-align: center;
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
