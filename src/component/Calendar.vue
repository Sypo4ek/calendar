<script setup>
  import { computed, ref, watchEffect } from 'vue'

  const { value } = defineProps(['value'])

  const date = ref(null)
  const selected = ref(null)
  const locale = ref('ru-RU')

  watchEffect(() => {
    if(typeof value === 'string' && !isNaN(Date.parse(value))){
      date.value = value 
      selected.value = new Date(value).getDate()
      return 
    }
      date.value = new Date().toISOString()
      selected.value =  new Date(new Date().toISOString()).getDate()
  })

  const handleSelect = (e)=>{
    locale.value = e?.target?.value
  }

  const handleClickDate = (value) => {
    if(!value) return
    selected.value = value
  }
  
  const handleClickAction = (num) => {
    const value = new Date(date.value)
    const newValue = new Date(value.setMonth(value.getMonth() + num))
    date.value = newValue.toISOString()
  }

  const daysInMonth = computed(()=>{
    const value = new Date(date.value);
    const emptyLength = new Date(value.setDate(1)).getDay() - 1 
    const emptyArray =  Array(emptyLength < 0 ? 0 : emptyLength);
    const daysArray = Array.from({length: new Date(value.getYear(), value.getMonth() + 1, 0).getDate()}, (_, id) => (id+1));
    return emptyArray.concat(daysArray);
  })

  const dayInWeek = computed(()=> Array(7).fill(undefined).map((_,i) => 
    new Date(new Date('2025.09.01').setDate(i+1)).toLocaleDateString(locale.value, { weekday: 'short' }))
  )

</script>

<template>
  <div class="container">
    <select name="lang" id="" @change="handleSelect">
    <option value="ru-RU">ru</option>
    <option value="en">en</option>
    <option value="fr-FR">fr</option>
    <option value="zh-Hans-CN">ch</option>
  </select>
  <div class="calendar">
    <div class="header">
      <button @click="handleClickAction(-1)">←</button>
      <h3> {{new Date(date).toLocaleString(locale, { month: 'short', year: 'numeric' }).replaceAll('.','').slice(0, -2)}}</h3>
      <button @click="handleClickAction(1)">→</button>
    </div>
    <div class="body">
      <p v-for="value in dayInWeek" class="weekday">{{ value }}</p>
      <a v-for="value in daysInMonth" class="day" :class="{empty:!value, active: value && selected === value}" @click="handleClickDate(value)">
        {{ value }}
      </a>
    </div>
  </div>
  </div>
</template>

<style scoped>
  select {
    width: 20rem;
    margin: 2rem auto 0;
    padding: .325rem;
    cursor: pointer;
    border-radius: .325rem;
  }

  select:hover{
    background-color: ghostwhite;
  }

  h3 {
    text-transform: capitalize;
  }

  button{
    padding: .325rem;
    cursor: pointer;
    background-color: white;
    border-radius: .325rem;
    border-width: .0625rem;
  }

  button:hover{
    background-color: whitesmoke;
  }

  .container{
    display: flex;
    flex-direction: column;
    width: 100dvw;
    height: 100dvh;
    align-items: center;
  }

  .calendar{
    overflow: hidden;
    display: flex;
    flex-direction: column;
    margin: 3rem auto auto;
    width: 25rem;
    height: 25rem;
    border-radius: 1rem;
    gap: 1rem;
    padding: 1rem;
    box-sizing: border-box;
    box-shadow: 0 0 .325rem .1rem rgba(138, 138, 138, 0.241);;
  }
  .header{
    height: 4.5rem;
    width: 100%;
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
  }

  .body{
    height: 20.5rem;
    width: 100%;
    display: grid;
    grid-template-columns: repeat(7, calc(100% / 8));
    grid-auto-rows: 2.5rem;
    gap: .125rem;
    align-items: center;
    justify-content: center;
  }

  .weekday{
    margin: 0;
    text-decoration: none;
    width: 2rem;
    height: 2rem;
    padding: .5rem;
    box-sizing: border-box;
    display: flex;
    align-items: center;
    justify-content: center;
    text-transform: capitalize;
    cursor: default;
  }

  .day{
    margin: 0;
    cursor: pointer;
    text-decoration: none;
    width: 2rem;
    height: 2rem;
    padding: .5rem;
    box-sizing: border-box;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: .325rem;
    transition: color .15s, background .3s;
  }

  .active{
    background: rgb(59, 136, 230);
    color: white;
  }

  .empty{
    cursor: default;
  }
</style>
