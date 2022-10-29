<template>
  <div class="line">
    <div type="text" class="letter" v-for="(letter,index) in letters" :key="index" v-bind:class="getClass(index)">
      {{ letter }}
    </div>
  </div>
</template>

<script>
export default {
  name: 'InputWord',
  props: {
    lettersCount: String,
    secretWord: String,
  },
  methods: {
    clear() {
      this.letters = Array(parseInt(this.lettersCount)).fill('');
    },
    printLetter(letter) {
      if (letter === 'Backspace') {
        this.removeLetter();
      }
      if (letter.length === 1) {
        this.addLetter(letter);
      }
    },
    addLetter(letter) {
      for (let i = 0; i < this.lettersCount; i++) {
        if (this.letters[i] === '') {
          this.letters[i] = letter;
          break;
        }
      }
      let wordFilled = this.letters.every((i) => i !== '');
      if (wordFilled) {
        this.$emit('event-word-filled', wordFilled, this.letters);
      }
    },
    removeLetter() {
      for (let i = this.lettersCount - 1; i >= 0; i--) {
        if (this.letters[i] !== '') {
          this.letters[i] = '';
          break;
        }
      }
      this.$emit('event-word-filled', false, this.letters);
    },
    getClass(index) {
      let secret = this.secretWord.split('');
      let secretLetter = secret[index].toLowerCase();
      let clientLetter = this.letters[index].toLowerCase();
      if (secretLetter === clientLetter) {
        return 'true';
      }
      if (secret.includes(clientLetter)) {
        return 'value';
      }
      return 'false';
    },
  },
  data: function () {
    return {
      letters: Array(parseInt(this.lettersCount)).fill(''),
      currentLetterPosition: 0,
    }
  }
}
</script>

<style scoped>
.line {
  display: flex;
  margin-bottom: 10px;
}

.letter {
  width: var(--letterBoxSize);
  height: var(--letterBoxSize);
  font-size: var(--letterSize);
  line-height: var(--letterBoxSize);
  font-weight: bold;
  color: #39638d;
  text-transform: uppercase;
  border: 1px solid #e0e0e0;
  margin-left: 5px;
  margin-right: 5px;
  border-radius: 4px;
}

.current-attempt .letter {
  border-color: #2c3e50;
}

.next-attempt .letter {
  background-color: #f0f0f0;
}

.previous-attempt .true {
  background-color: #fff93f;
}

.previous-attempt .false {
  background-color: #f0f0f0;
}
</style>
