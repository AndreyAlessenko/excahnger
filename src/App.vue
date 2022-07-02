<template>
  <div>
    <h1>Обмен валют</h1>
    <p> 1 {{ baseCurrencyName }} равно</p>
    <p> {{selectedCurrency.exchangeRatio}} {{ selectedCurrency.name }} равно</p>

    <input v-model="baseCurrency" placeholder="1"  />
<!--    <button @click="checkCurrency">Проверить валюту</button>-->
    <select v-model="selectedCurrency">
      <option  v-for="curr in Currencies" v-bind:value="curr">{{curr.name}}</option>
    </select>
    <p>{{chodenCurrent}}</p>
  </div>

</template>

<script>
export default {
  data(){
    return{
      baseCurrencyName: 'Российский рубль',
      baseCurrency: 1,
      exchangeRatio: 0.018,
      exchangeCurrencyName: 'Доллар США',
      selectedCurrency:{name:'Доллар США', exchangeRatio: 0.018},
      Currencies:[{
        name:'Доллар США', exchangeRatio: 0.018
      }]
    }
  },
  methods:{
    checkCurrency(){
      console.log(this.baseCurrency)
    }
  },
  computed:{
    chodenCurrent(){
      return this.selectedCurrency.exchangeRatio * this.baseCurrency
    }
  }
}
</script>

<style scoped>

</style>
