<script setup lang="ts">
import { ref, computed } from 'vue'

defineProps<{
  date: string
}>()

const position = ref(0)
const date = ref([
    new Array(2).fill(null),
    new Array(2).fill(null),
    new Array(4).fill(null)
])

const input = ref("")


const update = (e) => {
  e.preventDefault()
  
  console.log(e.target.value, e.target.selectionEnd, e.target.selectionStart, /\d/.test(e.key), e);

  const startPos = e.target.selectionStart

  if(startPos > 9) {
    return;
  }

  if(!/\d/.test(e.key)){ // Not a Digit
    return
  }

  e.target.style.caretColor = 'transparent'

  position.value = startPos === 2 ? 4 : startPos === 5 ? 7 : startPos+1
      
  const section = [
      startPos > 4 ? 2 : startPos > 1 ? 1 : 0,
      startPos === 2 || startPos === 5 ? 0 : startPos > 5 ? startPos - 6 : startPos > 2 ? startPos - 3 : startPos
  ]

  date.value[section[0]][section[1]] = e.key

  input.value = formattedValue()

}

const check = (e)=>{
  e.target.selectionStart = position.value
  e.target.selectionEnd = position.value
  e.target.style.caretColor = 'auto'
}

const formattedValue = () =>{
  const isEmpty = date.value.every(sec=>sec.every(val=>val===null))
  return isEmpty ? '' : date.value.map(section=>section.map(val=>val??'_').join('')).join('/')
}



const onfocus = (e)=>{
  e.target.placeholder = "__/__/____"
}

const onblur = (e) => {
  e.target.placeholder = "mm/dd/yyyy"
}

</script>



<template>
  <input
      placeholder="mm/dd/yyyy"
      v-model="input"
      @keydown.prevent="update"
      @keyup.prevent="check"
      @focus="onfocus"
      @blur="onblur"
      class="input-mask"
      />
</template>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .input-mask {
    padding: .35rem .75rem;
    letter-spacing: 1px;
    font-size: 1rem;
    line-height: 1.3rem;
  }
</style>
