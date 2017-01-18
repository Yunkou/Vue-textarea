<template>
  <div id="app">
    <img class="logo" src="http://ww3.sinaimg.cn/large/006y8lVagw1fbhebeyucrj305k05kmx4.jpg">
    <h1>{{ msg }}</h1>
    <h2>At Someone</h2>
    <ul class="at-some">
      <li v-for="p in people">
        <button @click="addAt(` @${p} `)">{{ p }}</button>
      </li>
    </ul>
    <div>{{atListIndex}}</div>
    <div class="area-box">
      <ul class="at-list" v-show="showAtlist" :style="atListPos">
        <li v-for="(p, index) in people" :class="{'current': atListIndex === index}">@{{ p }}</li>
      </ul>
      <textarea @click="bindClick" @keyup.delete="bindDel" v-model="textValue"
          @keyup.left="bindLeft" @keyup.right="bindRight" @keydown.50="logkey"
          @keydown.up="moveIndex($event, -1)" @keydown.down="moveIndex($event, 1)"
          @keydown.enter="enterAddAt($event)">
      </textarea>
    </div>
    <pre class="textarea"></pre>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      msg: 'Vue-textarea支持at和表情的输入框',
      people: ['路飞', '山治', '索隆', '娜美', '乔巴', '罗宾'],
      peoplePos: [],
      textValue: '',
      atListIndex: 0,
      showAtlist: false,
      textValueCopy: '',
      atListPos: {
        left: '0px',
        bottom: '150px'
      }
    }
  },
  methods: {
    addAt (p) {
      let textarea = document.querySelector('textarea')
      textarea.focus()
      let startPos = textarea.selectionStart
      let endPos = textarea.selectionEnd
      let cursorPos = startPos
      let tmpStr = this.textValue
      this.textValue = tmpStr.substring(0, startPos) + p + tmpStr.substring(endPos, tmpStr.length)
      cursorPos += p.length
      textarea.selectionStart = textarea.selectionEnd = cursorPos
      this.updatePos(this.textValue)
    },
    bindClick () {
      let textarea = document.querySelector('textarea')
      let startPos = textarea.selectionStart
      this.updatePos(textarea.value)
      let inPos = this.isInPos(startPos)
      if (inPos) {
        textarea.selectionStart = textarea.selectionEnd = inPos.end
      }
    },
    bindDel () {
      let textarea = document.querySelector('textarea')
      let startPos = textarea.selectionStart
      let inPos = this.isInPos(startPos)
      let tmpStr = textarea.value
      if (inPos) {
        textarea.value = tmpStr.substring(0, inPos.start + 1) + tmpStr.substring(inPos.end - 1, tmpStr.length)
        textarea.selectionStart = textarea.selectionEnd = inPos.start + 1
        this.peoplePos.splice(inPos.index, 1)
      }
      this.updatePos(textarea.value)
    },
    bindLeft () {
      let textarea = document.querySelector('textarea')
      let startPos = textarea.selectionStart
      this.updatePos(textarea.value)
      let inPos = this.isInPos(startPos)
      if (inPos) {
        textarea.selectionStart = textarea.selectionEnd = inPos.start
      }
    },
    bindRight () {
      let textarea = document.querySelector('textarea')
      let startPos = textarea.selectionStart
      this.updatePos(textarea.value)
      let inPos = this.isInPos(startPos)
      if (inPos) {
        textarea.selectionStart = textarea.selectionEnd = inPos.end
      }
    },
    isInPos (pos) {
      if (!this.peoplePos.length) { return false }
      let inPos = false
      this.peoplePos.forEach((el, index) => {
        if (pos > el.start && pos < el.end) {
          inPos = el
          inPos.index = index
          return
        }
      })
      return inPos
    },
    updatePos (val) {
      const REX = /\s@\S+\s/g
      let arr
      let posArr = []
      while ((arr = REX.exec(val)) !== null) {
        let pos = {
          start: arr.index,
          end: REX.lastIndex
        }
        posArr.push(pos)
      }
      this.peoplePos = posArr
    },
    logkey (e) {
      if (!e.shiftKey) { return }
      let index = document.querySelector('textarea').selectionStart
      let tmpStr = this.textValue
      let pre = document.querySelector('.textarea')
      pre.innerHTML = tmpStr.substring(0, index) + '<span></span>' + tmpStr.substring(index, tmpStr.length)
      let span = pre.querySelector('span')
      let left = span.offsetLeft - pre.offsetLeft
      let bottom = 150 - (span.offsetTop - pre.offsetTop)
      console.log(left, bottom)
      this.atListPos = {
        left: left + 'px',
        bottom: bottom + 'px'
      }
      this.showAtlist = true
    },
    moveIndex (e, n) {
      if (!this.showAtlist) { return }
      e.preventDefault()
      this.atListIndex += n
      if (this.atListIndex === -1) {
        this.atListIndex = 0
      }
      if (this.atListIndex > this.people.length - 1) {
        this.atListIndex = this.people.length - 1
      }

    },
    enterAddAt (e) {
      if (!this.showAtlist) { return }
      e.preventDefault()
      this.addAt(` @${this.people[this.atListIndex]} `)
      this.showAtlist = false
      this.atListIndex = 0
    }
  }
}
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 3px;
  }

  button {
    padding: 5px 16px;
    color: #2c3e50;
    background: #fff;
    border: 1px solid #42b983;
    border-radius: 10px;
  }

  .logo {
    width: 100px;
    height: 100px;
  }

  .area-box {
    position: relative;
    width: 375px;
    height: 150px;
    margin: 30px auto;
  }

  .at-list {
    margin: 0;
    position: absolute;
    width: 50px;
    left: -60px;
    border: 1px solid #42b983;
    max-height: 150px;
    overflow-y: auto;
    background: #ffffff;
  }

  .at-list li {
    margin: 0;
    padding: .2em 0;
    display: block;
    font-size: 12px;
    border-bottom: 1px solid #42b983;
  }

  .at-list li:last-child {
    border-bottom: 0;
  }

  .at-list li.current {
    background: #42b983;
    color: #ffffff;
  }

  textarea, .textarea {
    display: block;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    width: 375px;
    height: 150px;
    padding: 15px;
    margin: 0 auto;
    border: 1px solid #42b983;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
    text-align: left;
    font-size: 14px;
    overflow-y: auto;
    white-space: pre-wrap;
  }
  .textarea span {
    display: inline-block;
    width: 0;
    height: 14px;
    vertical-align: middle;
  }
  .textarea {
    visibility: hidden;
  }
</style>
