<!-- Custom Screen implementing the SelfPacedReading stimuli. Shows all sentences of a passage -->
<template>
  <Screen :progress="progress">
    <Slide v-for="element in presentationOrder">
      <SelfPacedReading
        v-if="!checkEndScreen(element)"
        :chunks="item[element].split(' ')"
        word-pos="next"
        underline="sentence"
        :response-times.sync="$magpie.measurements.times"
        @end="
          $magpie.addTrialData({
            itemID: item['ID'],
            type: element,
            setting: item[element + '_type'],
            phrase: item[element],
            phrase_length: item[element].split(' ').length,
            times: $magpie.measurements.times
          });
          $magpie.nextSlide();
        "
      />
      <SelfPacedReading
        v-else
        :chunks="item[element].split(' ')"
        word-pos="next"
        underline="sentence"
        :response-times.sync="$magpie.measurements.times"
        @end="$magpie.saveAndNextScreen()"
      />
      <Record
        :data="{
          itemID: item['ID'],
          type: element,
          setting: item[element + '_type'],
          phrase: item[element],
          phrase_length: item[element].split(' ').length
        }"
      />
    </Slide>
  </Screen>
</template>

<script>
export default {
  name: 'SelfPacedReadingScreen',
  props: ['item', 'progress'],
  computed: {
    // Sets the presentation order, some filler don't have a continuation
    presentationOrder() {
      if (this.item['continuation'].length == 0) return ['context', 'trigger'];
      else return ['context', 'trigger', 'continuation'];
    }
  },
  methods: {
    // Checks, whether the Screen should end or move to the next Slide
    checkEndScreen(i) {
      if (i === 'trigger' && this.item['continuation'].length == 0) return true;
      else if (i === 'continuation') return true;
      else return false;
    }
  }
};
</script>

<style scoped></style>
