<template>
  <div id="app">
    <img class="logo" src="http://ww3.sinaimg.cn/large/006y8lVagw1fbhebeyucrj305k05kmx4.jpg">
    <h1>{{ msg }}</h1>
<!--     <h2>Emoji</h2>
    <ul class="emoji-btn">
      <li><button>🎉</button></li>
      <li><button>🔖</button></li>
      <li><button>✨</button></li>
      <li><button>🐛</button></li>
    </ul> -->
    <h2>At Someone</h2>
    <ul class="at-some">
      <li v-for="p in people">
        <button @click="addAt(` @${p} `)">{{ p }}</button>
      </li>
    </ul>
    <div class="area-box">
      <textarea @click="bindClick" @keyup.delete="bindDel"
          @keyup.left="bindLeft" @keyup.right="bindClick">
      </textarea>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      msg: 'Vue-textarea支持at和表情的输入框',
      people: ['路飞', '山治', '索隆', '娜美', '乔巴', '罗宾'],
      peoplePos: []
    }
  },
  methods: {
    addAt (p) {
      let textarea = document.querySelector('textarea')
      textarea.focus()
      let startPos = textarea.selectionStart
      let endPos = textarea.selectionEnd
      let cursorPos = startPos
      let tmpStr = textarea.value
      let pos = {
        start: startPos,
        end: startPos + p.length
      }
      this.peoplePos.push(pos)
      textarea.value = tmpStr.substring(0, startPos) + p + tmpStr.substring(endPos, tmpStr.length)
      cursorPos += p.length
      textarea.selectionStart = textarea.selectionEnd = cursorPos
    },
    bindClick () {
      let textarea = document.querySelector('textarea')
      let startPos = textarea.selectionStart
      let inPos = this.isInPos(startPos)
      if (inPos) {
        textarea.selectionStart = inPos.end
      }
    },
    bindDel () {
      let textarea = document.querySelector('textarea')
      let startPos = textarea.selectionStart
      let inPos = this.isInPos(startPos)
      let tmpStr = textarea.value
      if (inPos) {
        textarea.value = tmpStr.substring(0, inPos.start) + tmpStr.substring(inPos.end, tmpStr.length)
        this.peoplePos.splice(inPos.index, 1)
      }
    },
    bindLeft () {
      let textarea = document.querySelector('textarea')
      let startPos = textarea.selectionStart
      let inPos = this.isInPos(startPos)
      let tmpStr = textarea.value
      if (inPos) {
        textarea.selectionStart = inPos.start
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
    width: 375px;
    height: 150px;
    margin: 30px auto;
  }

  textarea {
    display: block;
    width: 375px;
    height: 150px;
    padding: 15px;
    border: 1px solid #42b983;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
  }
</style>
