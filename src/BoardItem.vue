<template>
  <div class="item" :style="styleObject" v-on:click="set">
    <span class="text">{{value}}</span>
  </div>
</template>

<script>
export default {
  name: "BoardItem",

  props:{
    id:null
  },

  data(){
    return{
      value: null,
      whose: null,
      styleObject:{
        fontSize: '60px',
        position: 'relative',
        width: '100px',
        height: '100px',
        background: '#efefef',
        float: 'left',
        margin: '5px',
        cursor: 'pointer',
        display: 'inline-block'
      }
    }
  },

  methods:{
    set(item){
      this.$emit('setItem', this.id)
    }
  }
}
</script>

<style scoped>
</style>