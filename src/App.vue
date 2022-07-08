<template>
  <div class="card">
    <h1>Обмен валют</h1>
  </div>


  <div class='card'>
    <div v-if="exchangeRatio">
      <p> 1 {{ baseCurrencyName }} равно</p>
      <h3> {{ exchangeRatio }} {{ toCurrencyName }}</h3>
      <div class="grid">
        <input v-model="baseAmount" placeholder="Введите сумму" class="sum"/>
        <div>
          <select v-model="baseCurrency">
            <option v-for="curr in currencies" v-bind:value="curr.key" @change="getCoef()">{{ curr.value }}</option>
          </select>
          <button class="btn" @click="toggleCurrency">Изменить валюты</button>
        </div>

      </div>
      <div class="grid">
        <div class="sum">{{ result }}</div>
        <select v-model="toCurrency">
          <option v-for="curr in currencies" v-bind:value="curr.key" @change="getCoef()">{{ curr.value }}</option>
        </select>
      </div>
    </div>
    <div v-else>
      Подождите...
    </div>

  </div>

</template>

<script>
const jnltc = require('jnltc')
export default {
  data() {
    return {
      baseCurrency: 'RUB',
      baseCurrencyName: 'Russian Ruble',
      toCurrency: 'USD',
      toCurrencyName: 'United States Dollar',
      exchangeRatio: 0,
      baseAmount: 1,
      currencies: [],
      apiKey: '66fc79460a319d892127',
    }
  },
  mounted() {
    const myHeaders = new Headers();
    const requestOptions = {
      method: 'GET',
      redirect: 'follow',
      headers: myHeaders
    };

    fetch(`https://free.currconv.com/api/v7/currencies?apiKey=${this.apiKey}`, requestOptions)
        .then(response => response.text())
        .then(result => {
          const currArray = this.getCurrArray(result)
          this.setCurrToDD(currArray)
          this.setCurrPairByDefault()
          this.getCoef()
        })
        .catch(error => console.log('error', error));
  },
  methods: {
    toggleCurrency(){
      let buffer = this.baseCurrency
      this.baseCurrency = this.toCurrency
      this.toCurrency = buffer
      this.setCurrNames()
      this.getCoef()
    },
    getCurrArray(currString) {
      const currentObj = JSON.parse(currString).results
      let currArr = []
      for (let key in currentObj) {
        currArr.push({key: key, value: currentObj[key].currencyName})
      }
      return currArr
    },
    setCurrToDD(currArray) {
      this.currencies = currArray
    },
    setCurrPairByDefault() {
      this.baseCurrency = jnltc.localeToCurr(navigator.language).code
      this.toCurrency = 'USD'
      this.setCurrNames()
    },
    setCurrNames() {
      this.baseCurrencyName = this.getCurrNameFromID(this.baseCurrency)
      this.toCurrencyName = this.getCurrNameFromID(this.toCurrency)
    },
    getCoef() {
      if (!this.baseCurrency || !this.toCurrency) return
      const myHeaders = new Headers();
      const requestOptions = {
        method: 'GET',
        redirect: 'follow',
        headers: myHeaders
      };
      fetch(`https://free.currconv.com/api/v7/convert?q=${this.baseCurrency}_${this.toCurrency}&compact=ultra&apiKey=${this.apiKey}`, requestOptions)
          .then(response => response.text())
          .then(result => {
            this.exchangeRatio = JSON.parse(result)[this.baseCurrency + '_' + this.toCurrency].toFixed(2)
            this.setCurrNames()
          })
          .catch(error => console.log('error', error));
    },
    getCurrNameFromID(currID) {
      return this.currencies.find(item => item.key === currID).value
    }
  },
  computed: {
    result() {
      return (this.baseAmount * this.exchangeRatio).toFixed(2)
    },
  },
  watch: {
    baseCurrency() {
      this.getCoef()
    },
    toCurrency() {
      this.getCoef()
    }
  }
}
</script>

<style scoped>

</style>
