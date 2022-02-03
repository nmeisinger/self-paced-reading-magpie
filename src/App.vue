<template>
  <Experiment title="magpie demo">
    <!-- This example behaves as expected. First "Hello World" is displayed, then "Anybody here?"-->
    <Screen>
      <Slide>
        Hello World
        <button @click="$magpie.nextSlide()">Next slide</button>
      </Slide>

      <Slide>
        Anybody here?
        <button @click="$magpie.nextScreen()">Yes</button>
      </Slide>
    </Screen>

    <!-- This  behaves unexpected. Both slides are displayed at the same time -->
    <!-- Pressing the spacebar, advances both chunks arrays -->
    <Screen>
      <Slide>
        <SelfPacedReading
          :chunks="['this', 'is', 'the', 'first', 'slide']"
          word-pos="next"
          underline="sentence"
          :response-times.sync="$magpie.measurements.times"
          @end="$magpie.nextSlide()"
        />
      </Slide>
      <Slide>
        <SelfPacedReading
          :chunks="['this', 'is', 'the', 'second']"
          word-pos="next"
          underline="sentence"
          :response-times.sync="$magpie.measurements.times"
          @end="$magpie.nextScreen()"
        />
      </Slide>
      <!-- Test to see, if question_id can be stored for later -->
      <Record
        :data="{
          question_id: 1
        }"
      />
    </Screen>

    <ForcedChoiceScreen
      :options="['Yes', 'No']"
      question="Is this a text as well?"
    />

    <!-- Similar problem here. The second Screen's SelfPacedReading will start from the end index of the first one-->
    <Screen>
      <SelfPacedReading
        :chunks="['this', 'is', 'screen', '2']"
        word-pos="next"
        underline="sentence"
        :response-times.sync="$magpie.measurements.times"
        @end="$magpie.saveAndNextScreen()"
      />
    </Screen>

    <Screen>
      <SelfPacedReading
        :chunks="[
          'it',
          'will',
          'start',
          'from',
          'the',
          'fifth',
          'word',
          'instead',
          'of',
          'the',
          'beginning'
        ]"
        word-pos="next"
        underline="sentence"
        :response-times.sync="$magpie.measurements.times"
        @end="$magpie.saveAndNextScreen()"
      />
    </Screen>

    <ForcedChoiceScreen
      :options="['Yes', 'No']"
      question="Is this a question?"
    />

    <!-- With a ForcedChoiceScreen in-between, the SelfPacedReading works as expected -->
    <Screen>
      <SelfPacedReading
        :chunks="['normal', 'behavior', 'after', 'ForcedChoiceScreen']"
        word-pos="next"
        underline="sentence"
        :response-times.sync="$magpie.measurements.times"
        @end="$magpie.saveAndNextScreen()"
      />
    </Screen>

    <ForcedChoiceScreen
      :options="['Yes', 'No']"
      question="Is this a question?"
    />

    <Screen>
      <SelfPacedReading
        :chunks="[
          'normal',
          'behavior',
          'after',
          'ForcedChoiceScreen',
          'here',
          'as',
          'well'
        ]"
        word-pos="next"
        underline="sentence"
        :response-times.sync="$magpie.measurements.times"
        @end="$magpie.saveAndNextScreen()"
      />
    </Screen>

    <DebugResultsScreen />
  </Experiment>
</template>

<script>
// Load data from csv files as javascript arrays with objects
import forced_choice from '../trials/forced_choice.csv';
import multi_dropdown from '../trials/multi_dropdown.csv';
import sentenceChoice from '../trials/sentence_choice.csv';
import _ from 'lodash';

export default {
  name: 'App',
  components: {},
  data() {
    return {
      forced_choice,
      multi_dropdown,
      sentenceChoice,
      imageSelection: _.shuffle(imageSelection),
      sliderRating,

      // Expose lodash.range to template above
      range: _.range
    };
  }
};

const imageSelection = [
  {
    QUD: 'image selection - loop: 1, trial: 1',
    question: 'How are you today?',
    option1: 'fine',
    picture1: 'images/question_mark_02.png',
    option2: 'great',
    picture2: 'images/question_mark_01.png'
  },
  {
    QUD: 'image selection - loop: 1, trial: 2',
    option1: 'shiny',
    picture1: 'images/question_mark_03.jpg',
    option2: 'rainbow',
    picture2: 'images/question_mark_04.png'
  },
  {
    QUD: 'image selection - loop: 2, trial: 1',
    question: 'How are you today?',
    option1: 'fine',
    picture1: 'images/question_mark_03.jpg',
    option2: 'great',
    picture2: 'images/question_mark_01.png'
  },
  {
    QUD: 'image selection - loop: 2, trial: 2',
    option1: 'shiny',
    picture1: 'images/question_mark_02.png',
    option2: 'rainbow',
    picture2: 'images/question_mark_04.png'
  }
];

const sliderRating = [
  {
    picture: 'images/question_mark_02.png',
    question: 'How are you today?',
    optionLeft: 'fine',
    optionRight: 'great'
  },
  {
    picture: 'images/question_mark_01.png',
    question: "What's the weather like?",
    optionLeft: 'shiny',
    optionRight: 'rainbow'
  },
  {
    QUD: 'Here is a sentence that stays on the screen from the very beginning',
    picture: 'images/question_mark_03.jpg',
    question: "What's on the bread?",
    optionLeft: 'ham',
    optionRight: 'jam'
  }
];
</script>
