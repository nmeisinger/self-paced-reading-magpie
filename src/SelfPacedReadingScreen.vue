<template>
  <Screen>
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
          setting: item[element + '_type']
        }"
      />
    </Slide>
  </Screen>
</template>

<script>
export default {
  name: 'SelfPacedReadingScreen',
  props: {
    item: {
      type: Object,
      required: true
    }
  },
  computed: {
    presentationOrder() {
      if (this.item['continuation'].length == 0) return ['context', 'trigger'];
      else return ['context', 'trigger', 'continuation'];
    }
  },
  methods: {
    checkEndScreen(i) {
      if (i === 'trigger' && this.item['continuation'].length == 0) return true;
      else if (i === 'continuation') return true;
      else return false;
    }
  }
};
</script>

<style scoped></style>
