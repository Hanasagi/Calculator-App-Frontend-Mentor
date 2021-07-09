<template>
  <div id="main" ref="main">
    <header ref="header">
      <div>calc</div>
      <div class="themeSwitcher">
        <div class="themetxt">Theme</div>
        <div>
          <label for="1">1</label>
          <label for="2">2</label>
          <label for="3">3</label>
          <span>
            <input type="radio" value="1" name="theme" ref="first" @click="changeTheme">
            <input type="radio" value="2" name="theme" ref="second" @click="changeTheme">
            <input type="radio" value="3" name="theme" ref="third" @click="changeTheme">
          </span>
        </div>
      </div>
    </header>
    <div ref="answer" class="answer"><span v-if="current===0">0</span><span v-else>{{calc}}</span></div>
    <div ref="buttons" class="buttons">
      <input type="button" class="button" v-for="b in buttons" v-bind:key="b" :value="b" :ref="'b'+b" :class="'c'+b" />
    </div>

    <div class="attribution" ref="attribution">
      Challenge by <a href="https://www.frontendmentor.io?ref=challenge" target="_blank">Frontend Mentor</a>.
      Coded by <a href="#">Quentin Levenard</a>.
    </div>
  </div>
</template>


<script>
import './calculator.scss'
import "./third-theme-style.scss"

export default {
  name: 'HelloWorld',
  data: () => ({
    buttons: ['7', '8', '9', 'DEL', '4', '5', '6', '+', '1', '2', '3', '-', '.', '0', '/', 'x', 'RESET', '='],
    buttonsStr: ['7', '8', '9', 'DEL', '4', '5', '6', '+', '1', '2', '3', 'MINUS', 'DOT', '0', 'SLASH', 'x', 'RESET', 'EQ'],
    calc: "",
    current: 0,
    theme: ["first", "second", "third"]
  }),
  mounted() {
    this.$refs.main.setAttribute("theme", this.getPreferedTheme())
    this.$refs.buttons.childNodes.forEach((e) => {
      if (e.nodeType === 1) {
        let input = e.defaultValue
        if (input.match("[0-9.+-/x]")) {
          if (input.match("[.+-/x]"))
            e.className += ' button c' + e.defaultValue.charCodeAt()
          e.addEventListener("click", this.changeElem)
        } else if (input === "=") {
          e.addEventListener("click", this.validate)
          e.className += ' button c' + e.defaultValue.charCodeAt()
        } else if (input === "DEL") {
          e.addEventListener("click", this.delete)
        } else if (input === "RESET") {
          e.addEventListener("click", this.reset)
        }
      }
    })
  },
  methods: {
    getPreferedTheme() {
      let theme = localStorage.getItem('prefers-color-scheme');
      if(theme!==null){
        Object.keys(this.$refs).filter((ref) => {
          if (ref.includes(theme)) {
            return true
          }
          return false
        }).map((val) => {
          this.$refs[val].checked = true;
        })
        this.switchThirdThemeClass(theme)
      }else{
        localStorage.setItem('prefers-color-scheme','first')
        theme="first"
      }
      return theme;
    },
    changeElem(e) {
      this.current = 1
      let input = e.target.defaultValue;
      this.calc += input;
    },
    validate() {
      try {
        if (this.calc !== "") {
          this.calc = this.calculate(this.calc);
        }
      } catch (SyntaxError) {
        this.calc = "";
        this.current = 0;
      }
    },
    calculate(str) {
      return new Function('return ' + str)();
    },
    delete() {
      if (this.calc === "")
        this.current = 0
      else if (typeof(this.calc) === "string")
        this.calc = this.calc.slice(0, -1);
    },
    reset() {
      this.current = 0;
      this.calc = ""
    },
    changeTheme(e) {
      let newTheme = this.theme[e.target.value - 1];
      this.switchThirdThemeClass(newTheme)
      this.$refs.main.setAttribute("theme", newTheme);
      localStorage.setItem('prefers-color-scheme', newTheme)
    },
    switchThirdThemeClass(theme) {
      if (theme === "third") {
        console.log(this.$refs.buttons.childNodes)
        this.$refs.buttons.childNodes.forEach((e) => {
          if (e.classList != undefined) {
            this.changeClassList(e, "button")
            if (e.classList.contains("cDEL"))
              this.changeClassList(e, "cDEL")
            if (e.classList.contains("cRESET"))
              this.changeClassList(e, "cRESET")
            if (e.classList.contains("c61"))
              this.changeClassList(e, "c61")
          }
        })
        this.changeClassList(this.$refs.answer, "answer")
        this.changeClassList(this.$refs.attribution, "attribution")
        this.$refs.header.classList.add("thirdheader")
      } else {
        this.$refs.buttons.childNodes.forEach((e) => {
          if (e.classList != undefined) {
            this.revertClassList(e, "button")
            if (e.classList.contains("cDEL"))
              this.revertClassList(e, "cDEL")
            if (e.classList.contains("cRESET"))
              this.revertClassList(e, "cRESET")
            if (e.classList.contains("c61"))
              this.revertClassList(e, "c61")
          }
        })
        this.revertClassList(this.$refs.answer, "answer")
        this.revertClassList(this.$refs.attribution, "attribution")
        this.$refs.header.classList.remove("thirdheader")
      }
    },
    changeClassList(e, className) {
      e.classList.add("third" + className)
    },
    revertClassList(e, className) {
      e.classList.remove("third" + className)
      e.classList.add(className)
    }
  }
}

</script>

