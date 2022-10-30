<template>
  <h1>5 букв (Wordle)</h1>
  <div class="inputWord">
    <inputWord
        type="text"
        v-for="attempt in parseInt(numberOfAttempts)"
        :key="attempt"
        :ref="setRef(attempt)"
        :lettersCount="letters"
        :class="getClass(attempt)"
        v-bind:secretWord="secretWord"
        @event-word-filled="filledWordParse"
    />
    <button v-if="filledWord" @click="checkWord" class="btn btn-normal">Проверить</button>
    <button v-if="!filledWord && !youWin && !youLoose && !wordNotExist" @click="checkWord" class="btn btn-disabled"
            disabled>Проверить
    </button>
    <button v-if="youWin" @click="startNewGame" class="btn btn-success">Вы победили!</button>
    <button v-if="youLoose" @click="startNewGame" class="btn btn-fail">Вы проиграли :(</button>
    <button v-if="wordNotExist" class="btn btn-fail">Такого слова нет</button>
    <gameKeyboard
        :disabledLetters="Object.keys(disabledLetters)"
        @event-key-pressed="keyPress"
        ref="keyboard"
    />
    <div class="rules">
      <p>В начале игры появляется поле, состоящее из 30 клеток, по пять штук в строчках и по шесть штук в столбцах.</p>

      <p>В это поле можно вписать шесть слов, состоящих из пяти букв. Принимаются только существительные в единственнем
        числе.</p>

      <p>Ниже клавиатура на которой показывается статус букв.</p>

      <p>Начинайте вводить любое слово, как например слово «океан», нажмите на кнопку Ввод и буквы поменяют цвет.</p>

      <p>Расшифровка такая — желтая буква — слово на своем месте, белая — есть в слове но в другом месте, серая — буквы
        в слове нет.</p>
    </div>
  </div>
</template>

<script>
import InputWord from './InputWord.vue'
import GameKeyboard from './GameKeyboard.vue'
import {dictionary} from './dictionary.js'
import {secrets} from './secrets.js'

export default {
  name: 'FiveLetters',
  components: {
    InputWord, GameKeyboard
  },
  props: {
    numberOfAttempts: String,
    letters: String,
  },
  data: function () {
    return {
      currentAttempt: 1,
      filledWord: false,
      youWin: false,
      youLoose: false,
      wordNotExist: false,
      attempts: Array(this.numberOfAttempts).fill(''),
      disabledLetters: {},
      dictionary: dictionary,
      dictionaryShort: secrets,
      secretWord: '',
    };
  },
  computed: {
  },
  created() {
    window.addEventListener('keydown', (e) => {
      this.catchKeyPress(e.key);
    });
    this.secretWord = this.dictionaryShort[Math.floor(Math.random() * this.dictionaryShort.length)];
  },
  methods: {
    catchKeyPress(key) {
      let lowerKey = key.toLowerCase();
      if (this.disabledLetters[lowerKey]) {
        this.$refs['keyboard'].alertLetter(lowerKey);
        return;
      }
      this.$refs[this.setRef(this.currentAttempt)][0].printLetter(key);
      if (key === 'Enter' && this.filledWord) {
        this.checkWord();
      }
    },
    startNewGame() {
      this.currentAttempt = 1;
      this.filledWord = this.youWin = this.youLoose = this.wordNotExist = false;
      this.attempts = Array(this.numberOfAttempts).fill('');
      this.disabledLetters = {};
      let newWord = this.dictionaryShort[Math.floor(Math.random() * this.dictionaryShort.length)];
      this.secretWord = newWord;
      for (let i = 1; i <= this.numberOfAttempts; i++) {
        this.$refs[this.setRef(i)][0].clear();
      }
    },
    getClass(key) {
      if (key < this.currentAttempt) return 'previous-attempt';
      if (key === this.currentAttempt) return 'current-attempt';
      if (key > this.currentAttempt) return 'next-attempt';
    },
    setRef(key) {
      return 'attempt_' + key;
    },
    keyPress(key) {
      this.catchKeyPress(key);
    },
    filledWordParse(status, word) {
      this.filledWord = status;
      if (this.filledWord) {
        this.attempts[this.currentAttempt - 1] = word.join('').toLowerCase();
      }
    },
    checkWord() {
      this.filledWord = false;
      let attempt = this.attempts[this.currentAttempt - 1];
      if (!this.dictionary.includes(attempt)) {
        this.wordNotExist = true;
        window.setTimeout(() => {
          this.wordNotExist = false
        }, 1000);
        return;
      }
      this.fillBlacklistLetters(attempt);
      if (attempt === this.secretWord.toLowerCase()) {
        this.youWin = true;
      }
      if (attempt !== this.secretWord.toLowerCase() && this.currentAttempt === parseInt(this.numberOfAttempts)) {
        this.youLoose = true;
      } else {
        this.currentAttempt++;
      }
    },
    fillBlacklistLetters(word) {
      let letters = word.split('');
      letters.forEach(it => {
        if (!this.secretWord.includes(it)) {
          this.disabledLetters[it] = true;
        }
      });
    }
  }
}
</script>
<style scoped>
h1 {
  margin: 40px 0 0;
  font-size: 42px;
  color: #39638d;
  text-transform: capitalize;
}

.inputWord {
  width: calc(var(--letterSpacingSize) * 5);
  margin: 40px auto auto;
}

.btn {
  width: 100%;
  padding: 10px 10px;
  border: 0px;
  border-radius: 4px;
  font-size: 18px;
}

.btn-normal {
  background-color: #39638d;
  color: #ffffff;
}

.btn-success {
  background-color: #29a302;
  color: #ffffff;
}

.btn-fail {
  background-color: #a30202;
  color: #ffffff;
}

.rules {
  margin-top: 10px;
  font-size: 14px;
  text-align: left;
  border-top: 1px solid #f0f0f0;
}
</style>
