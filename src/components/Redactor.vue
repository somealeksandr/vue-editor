<template>
    <div class="container">
        <nav>
            <select v-model="fontSize" @change="changed($event, 'size')">
                <option :key="size" v-for="size in [12, 13, 14, 15, 16, 17, 40]" :value="size">{{ size }}</option>
            </select>
            <select v-model="fontColor" @change="changed($event, 'color')">
                <option value="red">Red</option>
                <option value="green">Green</option>
                <option value="blue">Blue</option>
            </select>
            <select v-model="backgroundColor" @change="changed($event, 'background')">
                <option value="red">Red</option>
                <option value="green">Green</option>
                <option value="blue">Blue</option>
            </select>
        </nav>
        <section id="redactor" @blur="update" contenteditable ref="redactor">
            <template v-for="(block, index) in contentArray">
                <redactorElement :key="index" :block="block" :index="index" @text-selected="textSelected"></redactorElement>
            </template>
        </section>
    </div>
</template>

<script>
import redactorElement from '@/components/Element'

export default {
  name: 'Redactor',
  components: {
    redactorElement
  },
  data () {
    return {
      contentArray: [],
      fontSize: '',
      fontColor: '',
      backgroundColor: '',
      buffer: {}
    }
  },
  methods: {
    update () {
      const parser = new DOMParser()
      const htmlDoc = parser.parseFromString(this.$refs.redactor.innerHTML, 'text/html')

      const childs = Array.prototype.slice.call(htmlDoc.body.children)

      let texts = []
      texts = childs.filter(item => item.tagName === 'DIV' && !item.classList.contains('redactor-div'))
      texts = texts.map(function (item) {
        return { styles: '', text: item.textContent, tag: 'p' }
      })
      if (texts.length) {
        this.$refs.redactor.innerHTML = ''
      }
      this.contentArray = [...this.contentArray, ...texts]
    },
    changed (value, property) {
      switch (property) {
        case 'size':
          this.buffer.parts[1].styles.size = `font-size: ${value.target.value}px;`
          break
        case 'color':
          this.buffer.parts[1].styles.color = `color: ${value.target.value};`
          break
        case 'background':
          this.buffer.parts[1].styles.background = `background: ${value.target.value};`
          break
      }

      this.contentArray[this.buffer.index].text = this.buffer.parts
    },
    textSelected (data) {
      const parts = []
      parts[0] = { text: data.text.split(data.selected)[0], styles: {} }
      parts[1] = { text: data.selected, styles: {} }
      const third = data.text.split(data.selected)[1]
      if (third) {
        parts[2] = { text: third, styles: {} }
      }
      this.buffer = { index: data.index, parts }
    }
  }
}
</script>

<style>
    .container {
        width: 100%;
        height: 100vh;
    }

    #redactor {
        width: 50%;
        height: 100%;
        background: #e8e8e8;
        float: left;
    }
</style>
