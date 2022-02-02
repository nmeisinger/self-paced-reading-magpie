<template>
  <Screen>
    <Slide>
      <SelfPacedReading
        :chunks="sentence"
        word-pos="next"
        underline="sentence"
        trigger="~"
        :response-times.sync="$magpie.measurements.times"
        @end="$magpie.saveAndNextScreen()"
      />
      <button @click="$magpie.saveAndNextScreen()">Next slide</button>
    </Slide>
  </Screen>
</template>

<script>
export default {
  name: 'SelfPacedReadingScreen',
  props: {
    sentence: {
      type: Array,
      required: true
    }
  }
};
</script>

<style scoped></style>
