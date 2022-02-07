<template>
  <main class="fixed w-full h-full bg-blue-100 flex items-center align-center">
    <div class="border border-black border-opacity-5 p-4 m-auto rounded-xl relative">
      <div id="hcalc_screen" ref="screen" class="text-3xl flex flex-col text-right border-b border-black border-opacity-5 mb-4 h-16 overflow-hidden flex items-center justify-end text-monospace">
        <div class="text-xl flex w-full h-6 justify-end items-center opacity-50">
          {{ previousOperand }} {{ operation }}
        </div>
        <div class="flex items-center justify-end w-full h-12">
          <span
            v-for="(char, charIdx) in currentOperand"
            :key="charIdx"
            :class="'c_' + char"
            v-text="char"
          />
        </div>
      </div>
      <KeyboardNumpad
        @keypress="onNumpadKeypress"
        @kpup="onNumpadKeyUp"
        @kpdown="onNumpadKeyDown"
      />
    </div>
    <audio ref="snd_keydown" :src="'/assets/keydown' + sndVarDown + '.wav'" hidden />
    <audio ref="snd_keyup" :src="'/assets/keyup' + sndVarUp + '.wav'" hidden />
  </main>
</template>

<script>
export default {
  name: 'IndexPage',
  data () {
    return {
      previousOperand: '',
      currentOperand: '',
      operation: null,
      sndVarDown: 1,
      sndVarUp: 1
    }
  },
  mounted () {
    this.$nextTick(() => {
      /** @type {HTMLDivElement} */
      const calcScreenEl = this.$refs.screen
      calcScreenEl.style.width = calcScreenEl.offsetWidth + 'px'
    })
  },
  methods: {
    /**
     * Define calculator keyboard commands and functions,
     * if the key isn't a special one then add it to the
     * current operand (this will be for number keys).
     */
    onNumpadKeypress (keyValue) {
      const self = this
      function addCharacterToOperand () {
        self.currentOperand = self.currentOperand + (typeof keyValue !== 'string' ? toString(keyValue) : keyValue)
      }
      const keyMethods = {
        /**
         * Clear all then AC is pressed.
         */
        backspace () {
          self.clearAll()
        },
        /**
         * Delete the last number on the current operand
         * when DEL is pressed.
         */
        delete () {
          /** @type {String} */
          let tempOperand = self.currentOperand
          tempOperand = tempOperand.split('')
          tempOperand.pop()
          self.currentOperand = tempOperand.join('')
        },
        '.' () {
          /** @type {String} */
          const thisOperand = self.currentOperand
          if (!thisOperand.length) {
            self.currentOperand += '0'
          }
          if (thisOperand.length > 0 && thisOperand.includes('.')) { return }
          addCharacterToOperand()
        },
        '-' () { self.determineOperation('substract') },
        '+' () { self.determineOperation('sum') },
        '*' () { self.determineOperation('multiply') },
        '/' () { self.determineOperation('divide') },
        '=' () {
          self.compute()
        }
      }
      /**
       * If a special key is pressed, do the function
       * then terminate this function to avoid key's
       * addition to current operand which only numbers
       * can be added.
       */
      if (keyMethods[keyValue]) {
        keyMethods[keyValue]()
        return
      }
      addCharacterToOperand()
    },
    /**
     * Play sound for keydown event.
     */
    onNumpadKeyDown () {
      this.playSnd('down')
    },
    /**
     * Play sound for keyup event.
     */
    onNumpadKeyUp () {
      this.playSnd('up')
    },
    /**
     * Play sound for a targeted event, which can be
     * "down" or "up".
     */
    playSnd (whichKey) {
      const self = this
      /* Audio elements */
      const els = {
        down: this.$refs.snd_keydown,
        up: this.$refs.snd_keyup
      }
      /* Audio randomize functions */
      const keys = {
        down () {
          self.sndVarDown = 1 + Math.floor(2 * Math.random())
        },
        up () {
          self.sndVarUp = 1 + Math.floor(2 * Math.random())
        }
      }
      /* Randomize sound for the target key */
      keys[whichKey]()
      /** @type {HTMLAudioElement} */
      const audioEl = els[whichKey]
      audioEl.pause()
      audioEl.currentTime = 0
      this.$nextTick(() => { audioEl.play() })
    },
    /**
     * Clear all the operations.
     */
    clearAll () {
      this.previousOperand = ''
      this.currentOperand = ''
      this.operation = null
    },
    determineOperation (operationType) {
      if (!this.currentOperand.length) { return }
      if (this.currentOperand.length && this.previousOperand.length && this.operation) {
        this.compute()
      }
      this.previousOperand = this.currentOperand
      this.currentOperand = ''
      const operationSymbols = {
        sum: '+',
        substract: '-',
        multiply: '*',
        divide: '/'
      }
      this.operation = operationSymbols[operationType]
    },
    compute () {
      if (!this.currentOperand.length || !this.operation) { return }
      let result = 0
      const self = this
      const operationDefinitions = {
        '+': () => parseFloat(self.previousOperand) + parseFloat(self.currentOperand),
        '-': () => parseFloat(self.previousOperand) - parseFloat(self.currentOperand),
        '*': () => parseFloat(self.previousOperand) * parseFloat(self.currentOperand),
        '/': () => parseFloat(self.previousOperand) / parseFloat(self.currentOperand)
      }
      result = operationDefinitions[this.operation]()
      this.currentOperand = !Number.isNaN(result)
        ? !Number.isInteger(result)
            ? result.toFixed(2).toString()
            : result.toString()
        : ''
      this.previousOperand = ''
      this.operation = null
    }
  }
}
</script>

<style scoped>
#hcalc_screen .c_\+,
#hcalc_screen .c_\-,
#hcalc_screen .c_\*,
#hcalc_screen .c_\/ {
  @apply text-blue-500 font-bold px-2;
}

#hcalc_screen .c_\. {
  @apply text-blue-300 font-bold px-1;
}
</style>
