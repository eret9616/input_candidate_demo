<template>
  <div class="wrapper" 
        v-show="show" 
        :style='candidateStyle' 
        @mousedown="handleMouseDown">
      {{candidateText}}
      <ul>
          <li 
          v-for="(item,index) in ['A','B','C']" 
          :key='index'
          @click.prevent="handleSelectText($event,item)"
          >
              {{item}}
          </li>
      </ul>
  </div>
</template>

<script>
export default {
    data(){
        return {
            // show:false
            show:true
        }
    },
    props:['top','left','candidateText'],
    computed:{
        candidateStyle(){
            return {
                position:'absolute',
                top:this.top+'px',
                left:this.left+'px'
            }
        },
    },
    watch:{
        candidateText:{
            immediate:true,
            deep:true,
        }
    },
    methods:{
        handleMouseDown(e){
            e.preventDefault();
        },
        handleSelectText(e,text){
            console.log(text);
            this.$emit('emitText',text)
        }
    }
}
</script>

<style scoped>
.wrapper{
    width:100px;
    height:150px;
   border:1px solid black; 
   /* background: red; */
}

</style>