<template>
  <Experiment title="magpie demo">
    <template v-for="item in itemList">
      <SelfPacedReadingScreen :item="item" />
      <KeypressScreen
        :question="item['question']"
        :keys="{
          f: 'yes',
          j: 'no'
        }"
      >
        <template #stimulus>
          <Record
            :data="{
              question: item['question'],
              itemID: item['ID'],
              correct_answer: item['correct_answer']
            }"
          />
        </template>
      </KeypressScreen>
    </template>
    <DebugResultsScreen />
  </Experiment>
</template>

<script>
import _ from 'lodash';
import itemsNoFiller from '../trials/data_non_filler.json';
import itemsFiller from '../trials/data_filler.json';
import latinSquare from '../trials/presentation_lists.json';
import SelfPacedReadingScreen from './SelfPacedReadingScreen';

export default {
  name: 'App',
  components: { SelfPacedReadingScreen },
  data() {
    return {
      // Expose lodash.range to template above
      range: _.range,
      items: itemsNoFiller,
      fillerItems: itemsFiller,
      presentationLists: latinSquare
    };
  },
  computed: {
    itemList() {
      let nonFillerItemList = [];
      const presentationList = _.sample(this.presentationLists);
      const itemListRandomized = _.shuffle(this.items);

      for (let i = 0; i < presentationList.length; i++) {
        let item = itemListRandomized[i];
        let presentationListItem = presentationList[i];
        nonFillerItemList.push(
          this.createNonFiller(item, presentationListItem)
        );
      }

      for (let i = 0; i < this.fillerItems.length; i++) {
        let item = this.fillerItems[i];
        nonFillerItemList.push(this.createFiller(item));
      }
      nonFillerItemList = _.shuffle(nonFillerItemList);
      return nonFillerItemList;
    }
  },
  methods: {
    createNonFiller(item, presentationListItem) {
      let result = {
        ID: item['ID'],
        type: item['type'],
        context_type: presentationListItem['context_type'],
        context: item['context'][presentationListItem['context_type']],
        trigger_type: presentationListItem['trigger_type'],
        trigger: item['trigger'][presentationListItem['trigger_type']],
        continuation_type: presentationListItem['continuation_type'],
        continuation:
          item['continuation'][presentationListItem['continuation_type']],
        question: item['question'],
        correct_answer: item['correct']
      };
      return result;
    },
    createFiller(item) {
      let result = {
        ID: item['ID'] + 'f',
        type: item['type'],
        context_type: 'filler',
        context: item['context'],
        trigger_type: 'filler',
        trigger: item['trigger'],
        continuation_type: 'filler',
        continuation: item['continuation'],
        question: item['question'],
        correct_answer: item['correct']
      };
      return result;
    }
  }
};
</script>
