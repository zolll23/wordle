<template>
  <div class="keyboard">
    <div class="line" v-for="(line,index) in letters" :key="index">
      <div class="key" v-bind:class="getClass(key)"
           v-for="(key,index) in line" :key="index" @click="pressKey(key)">{{ key }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameKeyboard',
  props: {
    disabledLetters: Array,
  },
  methods: {
    pressKey(key) {
      if (this.checkDisabled(key)) {
        return;
      }
      this.$emit('event-key-pressed', key === 'Del' ? 'Backspace' : key);
    },
    alertLetter(key) {
      this.alertKey = key;
    },
    getClass(key) {
      if (this.alertKey === key) {
        window.setTimeout(() => {
          this.alertKey = ''
        }, 500);
        return 'alert';
      }
      return this.checkDisabled(key) ? 'disabled' : '';
    },
    checkDisabled(key) {
      return this.disabledLetters.join().includes(key);
    },
  },
  data: function () {
    return {
      alertKey: '',
      letters: [
        ['й', 'ц', 'у', 'к', 'е', 'н', 'г', 'ш', 'щ', 'з', 'х'],
        ['ф', 'ы', 'в', 'а', 'п', 'р', 'о', 'л', 'д', 'ж', 'э'],
        ['я', 'ч', 'с', 'м', 'и', 'т', 'ь', 'б', 'ю', 'Del']
      ],
    }
  }
}
</script>

<style scoped>
.keyboard {
  margin-top: 20px;
  width: 100%;
}

.line {
  width: 100%;
  display: flex;
  margin-bottom: 10px;
}

.key {
  cursor: pointer;
  min-width: 22px;
  height: 22px;
  font-size: 12px;
  line-height: 22px;
  font-weight: bold;
  color: #39638d;
  text-transform: uppercase;
  border: 1px solid #e0e0e0;
  margin-left: 1px;
  margin-right: 1px;
  border-radius: 4px;
  webkit-touch-callout: none;
  -webkit-user-select: none;
  -khtml-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.key:last-child {
  width: 52px;
}

.disabled {
  background-color: #f0f0f0;
  color: #a0a0a0;
  cursor: not-allowed;
}

.alert {
  background-color: #c70303;
  color: #ffffff;
  cursor: not-allowed;
}
</style>
