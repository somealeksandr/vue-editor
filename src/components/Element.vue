<template>
    <div v-if="block" class="redactor-div" v-html="prepareBlock()"></div>
</template>

<script>
export default {
  name: 'Element',
  props: {
    block: {},
    index: {
      type: Number,
      index: ''
    }
  },
  mounted () {
    const i = `element_${this.index}`
    document.addEventListener('mouseup', event => {
      if (event.target.getAttribute('ref') === i) {
        this.selectFunction()
      }
    })
  },
  computed: {
    text () {
      if (Array.isArray(this.block.text)) {
        let string = ''
        this.block.text.forEach(function (part) {
          let styles = ''
          if (part.styles.size || part.styles.color || part.styles.background) {
            styles = (part.styles.size ? part.styles.size : '') + ' ' + (part.styles.color ? part.styles.color : '') + ' ' + (part.styles.background ? part.styles.background : '')
          }
          if (styles) {
            string += `<span style="${styles}">${part.text}</span>`
          } else {
            string += part.text
          }
        })
        return string
      }

      return this.block.text
    }
  },
  methods: {
    prepareBlock () {
      if (this.block) {
        return `<${this.block.tag} ref="element_${this.index}" style="${this.block.styles}">${this.text}</${this.block.tag}>`
      }
    },
    selectFunction () {
      this.$emit('text-selected', { block: this.block, text: this.text, index: this.index, selected: window.getSelection().toString() })
    }
  }
}
</script>
