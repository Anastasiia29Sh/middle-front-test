<template>
  <div class="input-with-suggestions">
    <v-text-field
      ref="inputField"
      v-model="inputValue"
      :error-messages="errorMessage"
      :label="label"
      :rules="rules"
      @input="updateSuggestions"
      @keydown.tab.prevent="completeSuggestion"
    />

    <span ref="hiddenElement" class="hidden-element">{{ inputValue }}</span>

    <div
      v-if="suggestion && inputValue.length"
      class="suggestion"
      :class="{ visible: suggestion }"
      :style="{ left: suggestionOffset + 'px' }"
    >
      {{ suggestion.slice(inputValue.length) }}
    </div>
  </div>
</template>

<script>
  export default {
    name: 'InputWithSuggestions',

    props: {
      label: {
        type: String,
        default: 'Введите текст',
      },
      suggestions: {
        type: Array,
        required: true,
      },
      rules: {
        type: Array,
        default: () => [],
      },
    },

    data: () => ({
      inputValue: '',
      errorMessage: [],
      suggestion: null,
      suggestionOffset: 0,
    }),

    methods: {
      updateSuggestions () {
        const matchingSuggestions = this.suggestions.filter(word =>
          word.toLowerCase().startsWith(this.inputValue.toLowerCase())
        )

        this.suggestion = matchingSuggestions.length ? matchingSuggestions[0] : null

        this.calculateSuggestionOffset()

        this.validateInput()

        this.$emit('change-value', this.inputValue)
      },

      completeSuggestion () {
        if (this.suggestion) {
          this.inputValue = this.suggestion
          this.suggestion = null
          this.$emit('change-value', this.inputValue)
        }
      },

      validateInput () {
        this.errorMessage = []
        for (const rule of this.rules) {
          const result = rule(this.inputValue)
          if (result !== true) {
            this.errorMessage.push(result)
          }
        }
      },

      calculateSuggestionOffset () {
        if (!this.$refs.hiddenElement) return

        const inputWidth = this.$refs.hiddenElement.offsetWidth
        const paddingLeft = 18
        this.suggestionOffset = inputWidth + paddingLeft
      },
    },
  }
</script>

<style scoped>
.input-with-suggestions {
  position: relative;
}

.hidden-element {
  position: absolute;
  visibility: hidden;
  white-space: pre;
  font-family: inherit;
  font-size: inherit;
  font-weight: inherit;
}

.suggestion {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  color: #aaa;
  pointer-events: none;
  opacity: 0;
  transition: opacity 0.3s;
}

.suggestion.visible {
  opacity: 1;
}
</style>
