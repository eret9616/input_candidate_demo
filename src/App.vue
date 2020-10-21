<template>
  <div id="app">
    <input 
    ref='ipt'
      id='input' type="text" 
      v-model='inputText' 
      @focus="handleFocus" 
      @blur="handleBlur" 
      @input="handleInput" 
      @click="handleClick" 
      @keyup="handleKeyUp"
      @mouseleave="hanldeMouseLeave"
      >

    <div>fmtRect:{{fmtRect}}</div>
    <div>inputText:{{inputText}}</div>
    <div>inputWidth:{{inputTextWidth}}</div>
    <div>inputScrollLeft:{{inputScrollLeft}}</div>
    <div>inputScrollWidth:{{inputScrollWidth}}</div>
    <div>focusWidth:{{focusTextWidth}}</div>
    <div>candidateLeft:{{candidateLeft}}</div>
    <div>selectionStart:{{selectionStart}}</div>
    <div>selectionEnd:{{selectionEnd}}</div>
    <div>{{inputTextWidth}}</div>


          <candidate 
            v-show="!selectionStartNotEqualSelectionEnd"
            :top='candidateTop' 
            :left='candidateLeft'
            :candidateText='inputText && inputText.slice(-1)'
            @emitText='handleEmitText'
          />


  </div>
</template>

<script>
import candidate from './candidate'

function getTextWidth(text, font) {
    // re-use canvas object for better performance
    var canvas = getTextWidth.canvas || (getTextWidth.canvas = document.createElement("canvas"));
    var context = canvas.getContext("2d");
    context.font = font;
    var metrics = context.measureText(text);
    return metrics.width;
}

export default {
  name: 'App',
  components: {
    candidate
  },
  data(){
    return{
      inputScrollLeft:undefined,
      inputScrollWidth:undefined,
      // 位置信息
    rect:undefined,
    //
    offsetInfo:undefined,
    // 输入框的shuju
    inputText:undefined,
    // 
    selectionStart:undefined,
    selectionEnd:undefined,// 当start与end不同时应该不显示
    // 字体
    font:undefined
    }

  },
  computed:{
    selectionStartNotEqualSelectionEnd(){
      return this.selectionStart !== this.selectionEnd
    },
    fmtRect(){
      return JSON.stringify(this.rect)
    },
    inputTextWidth(){
      return getTextWidth(this.inputText) || 0
    },
    focusTextWidth(){
      if(this.selectionStart){
        return  getTextWidth(this.inputText.slice(0,this.selectionStart),this.font)
      }
       return 0
    },
    candidateTop(){
      if(this.fmtRect){
        let fmtRect = JSON.parse(this.fmtRect)
        return fmtRect.y + fmtRect.height
      }
      return undefined
    },
    candidateLeft(){
      
      let _focusTextWidth = this.focusTextWidth
      if(this.fmtRect){
        const fmtRect = JSON.parse(this.fmtRect)
          // 超出边界、滚动情况处理
          if(this.inputScrollLeft){
             return fmtRect.x + _focusTextWidth - this.inputScrollLeft
        }
        return fmtRect.x + _focusTextWidth
      }
      return undefined
    }
  },
  methods:{
    handleEmitText(val){
      const start = this.inputText.slice(0,this.selectionStart)
      const end = this.inputText.slice(this.selectionStart)
     const oldSelectionStart = this.selectionStart
     const step = val.length

      this.inputText= start + val +end;
      // console.log('===========');
      // console.log(iptDOM);
      // console.log('===========');

      this.$nextTick(()=>{
       const iptDOM = this.$refs.ipt
       iptDOM.selectionStart=oldSelectionStart +step
       iptDOM.selectionEnd=oldSelectionStart +step
        const e ={
          target:iptDOM,
          oldPosition:this.selectionStart
      } 
       this.getTargetRect(e)
      })
    },
    handleKeyUp(e){
      this.getTargetRect(e)
    },
    handleClick(e){
      this.getTargetRect(e)
    },
    handleFocus(e){
      // const rect = this.getTargetRect(e,true)
    },
    handleBlur(e){
      console.log('blur');
    },
    handleInput(e){
      this.getTargetRect(e)
    },
    getTargetRect(e,noSelection){
        let domTarget = e.target
        // 处理成响应式对象
        if(!this.SET){
            const obj  ={a:1,b:2}



            this.SET= true
        }





        const rect = domTarget.getBoundingClientRect()
        this.inputScrollLeft = domTarget.scrollLeft
        this.inputScrollWidth = domTarget.scrollWidth
        this.font = window.getComputedStyle(domTarget).font
        if(!noSelection)
        this.selectionStart = domTarget.selectionStart
        this.selectionEnd = domTarget.selectionEnd
        this.rect = rect
        return rect
    },
    // 鼠标直接划出的时候，也要计算，选择了框内文本会出现这种情况
    hanldeMouseLeave(e){
       this.getTargetRect(e)
    }
  }
}
</script>

<style>
#app{
  width:600px;
  height:1200px;
  border:1px solid red;
}

#input{
  margin-left:123px;
  margin-top:100px;
}
</style>
