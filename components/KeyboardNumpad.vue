<template>
  <div class="flex flex-col gap-5">
    <div id="hcalc_kp_acts" class="flex items-center justify-end gap-x-5">
      <btn
        v-for="(keyval, keyIdx) in actionsMap"
        :ref="'k_' + keyval.key"
        :key="keyIdx"
        :value="keyval.value"
        width="md"
        @click.native="onKeypress"
      >
        <span class="pointer-events-none select-none" v-text="keyval.label" />
      </btn>
    </div>
    <div class="flex items-start justify-center gap-x-5">
      <div id="hcalc_kp_main" class="grid grid-cols-3 gap-5">
        <btn
          v-for="(keyval, keyIdx) in mainMap"
          :ref="'k_' + keyMap[keyIdx]"
          :key="keyIdx"
          :value="keyval"
          @click.native="onKeypress"
        >
          <span class="pointer-events-none select-none" v-text="keyval" />
        </btn>
      </div>
      <div id="hcalc_kp_side" class="gap-y-5 flex flex-col">
        <btn
          v-for="(keybtn, keyIdx) in sideMainMap"
          :ref="'k_' + keybtn.key"
          :key="keyIdx"
          :value="keybtn.value"
          @click.native="onKeypress"
        >
          <span class="pointer-events-none select-none" v-text="keybtn.key" />
        </btn>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CalcKeyboardNumpad',
  data () {
    return {
      actionsMap: [
        {
          value: 'backspace',
          key: 'Backspace',
          label: 'AC'
        },
        {
          value: 'delete',
          key: 'Delete',
          label: 'DEL'
        }
      ],
      mainMap: [
        7, 8, 9,
        4, 5, 6,
        1, 2, 3,
        0, '.', '='
      ],
      sideMainMap: [
        {
          value: '-',
          key: '-'
        },
        {
          value: '+',
          key: '+'
        },
        {
          value: '*',
          key: '*'
        },
        {
          value: '/',
          key: '/'
        }
      ],
      keyMap: [
        '7', '8', '9',
        '4', '5', '6',
        '1', '2', '3',
        '0', '.', 'Enter',
        '-', '+', '/', '*',
        'Delete', 'Backspace'
      ]
    }
  },
  mounted () {
    this.$nextTick(() => {
      window.onkeydown = this.onKeyboardKeydown
      window.onkeyup = this.onKeyboardKeyup
    })
  },
  methods: {
    /**
     * Method for "onclick" event for numpad buttons.
     * @param e {MouseEvent}
     */
    onKeypress (e) {
      const buttonKeyVal = e.target.value
      this.$emit('keypress', buttonKeyVal)
    },
    /**
     * Method for "onkeydown" event for window.
     * @param e {KeyboardEvent}
     */
    onKeyboardKeydown (e) {
      if (this.keyMap.includes(e.key)) {
        /** @type {Vue} */
        const btnComponent = this.$refs['k_' + e.key][0]
        /** @type {HTMLButtonElement} */
        const btnEl = btnComponent.$el
        btnEl.click()
        btnEl.classList.add('btn_active')
        this.$emit('kpdown')
      }
    },
    /**
     * Method for "onkeyup" event for window.
     * @param e {KeyboardEvent}
     */
    onKeyboardKeyup (e) {
      if (this.keyMap.includes(e.key)) {
        /** @type {Vue} */
        const btnComponent = this.$refs['k_' + e.key][0]
        /** @type {HTMLButtonElement} */
        const btnEl = btnComponent.$el
        btnEl.classList.remove('btn_active')
        this.$emit('kpup')
      }
    }
  }
}
</script>
