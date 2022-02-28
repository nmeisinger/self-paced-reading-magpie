<template>
  <Experiment title="Self-paced Reading Experiment">
    <InstructionScreen>
      <p>Hello and welcome to this experiment!</p>
      <p>
        This study is concerned with how humans read und interpret language.<br />
        For this, you will be presented with a variety of sentences for which
        you will have to answer comprehension questions.
      </p>
      <p>The estimated duration is 15-25 minutes.</p>
      <p>
        Personal data will not be recorded and participation in this experiment
        is voluntary. At the end, you will another opportunity to decide,
        whether you answers should be saved or not.
      </p>
      <p>
        If you agree with these terms, press the button below to receive further
        instructions about the experiment.
      </p>
    </InstructionScreen>

    <InstructionScreen>
      <p>
        You will be presented with passages of 2-3 sentences. After each passage
        you will need to answer a comprehension question.
      </p>

      <p>
        Place your left index finger on the 'f' key, the right index finger on
        the 'j' key. Place one or both of your thumbs on the spacebar.
      </p>

      <p>Try to read each sentence as fast and accurate possible.</p>
      <p>
        Once you click on the button below, a trial will start, so you can get
        accustomed to the flow of the experiment.
      </p>
    </InstructionScreen>

    <SelfPacedReadingScreen :item="itemListTrial[0]" />
    <KeypressScreen
      :question="itemListTrial[0]['question']"
      :keys="{
        f: 'no',
        j: 'yes'
      }"
    >
      <template #stimulus>
        <Record
          :data="{
            question: itemListTrial[0]['question'],
            itemID: itemListTrial[0]['ID'] + 'trial',
            correct_answer: itemListTrial[0]['correct_answer']
          }"
        />
      </template>
    </KeypressScreen>

    <InstructionScreen>
      <p>You completed the trial.</p>
      <p>Once you press the button below, the real experiment will begin.</p>
    </InstructionScreen>

    <template v-for="(item, index) in itemList">
      <InstructionScreen
        v-if="
          index == ~~(itemList.length / 3) ||
          index == ~~((itemList.length * 2) / 3)
        "
      >
        <p>
          Please feel free to take a short break before continuing with this
          experiment.
        </p>
      </InstructionScreen>

      <SelfPacedReadingScreen
        :item="item"
        :progress="index / itemList.length"
      />
      <KeypressScreen
        :question="item['question']"
        :keys="{
          f: 'no',
          j: 'yes'
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

    <InstructionScreen>
      <p>Thanks for completing this experiment!</p>
      <p>Once you press the button below, your data will be submitted.</p>
    </InstructionScreen>
    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
import _ from 'lodash';
import itemsNoFiller from '../trials/data_non_filler.json';
import itemsFiller from '../trials/data_filler.json';
import itemsFillerTrial from '../trials/trial.json';
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
      fillerItemsTrial: itemsFillerTrial,
      presentationLists: latinSquare
    };
  },
  computed: {
    itemList() {
      let finalItemList = [];
      const presentationList = [].concat.apply(
        [],
        _.sampleSize(this.presentationLists, 4)
      );
      const itemListRandomized = _.shuffle(this.items);

      // add non-filler to finalItemList, according to selected presentation
      // lists
      for (let i = 0; i < presentationList.length; i++) {
        let item = itemListRandomized[i];
        let presentationListItem = presentationList[i];
        finalItemList.push(this.createNonFiller(item, presentationListItem));
      }

      // add filler items to finalItemList
      for (let i = 0; i < this.fillerItems.length; i++) {
        let item = this.fillerItems[i];
        finalItemList.push(this.createFiller(item));
      }
      // randomize order
      finalItemList = _.shuffle(finalItemList);
      return finalItemList;
    },
    itemListTrial() {
      let itemList = [];
      for (let i = 0; i < this.fillerItemsTrial.length; i++) {
        let item = this.fillerItemsTrial[i];
        itemList.push(this.createFiller(item));
      }
      return itemList;
    }
  },
  methods: {
    // create non-filler objects
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
    // create filler objects
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
