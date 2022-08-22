<template>
<div class="main">
  <div>
    <p>Актуальний курс валют Приватбанку</p>
    <table>
      <thead>
        <tr>
          <th>Поточний курс</th>
          <th>Валюта</th>
          <th>Купівля</th>
          <th>Продаж</th>
        </tr>
      </thead>
      <tbody v-for="item in  currencyPrice" :key="item.ccy">
        <tr>
          <td>{{ item.base_ccy }}</td>
            <td>{{ item.buy }}</td>
            <td>{{ item.ccy }}</td>
            <td>{{ item.sale }}</td>
        </tr>
      </tbody>
    </table>
  </div>
  <div class="inputs">
    <div>
    <div>
      <input class="input" type="number" v-model="sum1" @focus="zeroingInputTwo">
    </div>
    <div>
      <select name="currency" 
      v-model="currencyDataToConvert"
      @click="zeroingInputTwo">
        <option 
          v-for="currency in currencyPrice"
          :key="currency.ccy ">
            {{currency.ccy }}
        </option>
      </select>
    </div> 
    </div>
    <div>
    <div>
      <input class="input" type="number" v-model="sum2" @focus="zeroingInputOne" >
    </div>
    <div>
      <select name="currency" 
        v-model="currencyDataFromConvert"
        @click="zeroingInputOne">
        <option 
        @click=" ratesSale(currency.sale)"
          v-for="currency in currencyPrice"
          :key="currency.ccy ">
            {{currency.ccy }}
        </option>
      </select>
    </div> 
  </div>
  </div>
  <div class="button">
    <button class="convert" @click="convert">Convert</button>
  </div>
  <div class="date">{{new Date()}}</div> 
</div> 
</template>

<script>
import axios from 'axios'
export default {
  name: 'App',
  components: {

  },

  data(){

    return{
    currencyPrice: [],
    currencyDataToConvert:"USD",
    currencyDataFromConvert:"USD",
    sum1:0,
    sum2:0,
  }

},
   mounted() {
    axios.get("https://api.privatbank.ua/p24api/pubinfo?json&exchange&coursid=5")
      .then(response => { this.currencyPrice = response.data,
        console.log(this.currencyPrice)
      })
      .catch(function (error) {
          console.log(error);
    });

},
computed:{
  rateBay() {
    return this.currencyPrice.filter( elem =>
    elem.ccy == this.currencyDataToConvert)
  },
  rateSale() {
     return this.currencyPrice.filter( elem =>
    elem.ccy == this.currencyDataFromConvert)
  }
},
methods: {
    convert(){ 
   if(this.sum1 != 0 && this.sum2 == 0) {  
    if(this.rateBay[0].ccy != this.rateSale[0].ccy && this.rateBay[0].ccy != "BTC" &&
        this.rateSale[0].ccy != "BTC"){
        this.sum2 = (this.sum1 * this.rateBay[0].buy) / this.rateSale[0].sale
    }
    else if (this.rateBay[0].ccy != this.rateSale[0].ccy && this.rateBay[0].ccy == "BTC"){
            this.sum2 = (this.sum1 * this.rateBay[0].buy * 39.5 ) / this.rateSale[0].sale
    }
    else if (this.rateBay[0].ccy != this.rateSale[0].ccy  && this.rateSale[0].ccy == "BTC"){
      this.sum2 = (this.sum1 * this.rateBay[0].buy) /(this.rateSale[0].sale * 39.9 )
    }
    else {
      this.sum2 = this.sum1
    }
  }
  else if (this.sum1 == 0 && this.sum2 != 0) {
    if(this.rateSale[0].ccy != this.rateBay[0].ccy && this.rateSale[0].ccy != "BTC" &&
        this.rateBay[0].ccy != "BTC"){
        this.sum1 = (this.sum2 * this.rateSale[0].buy) / this.rateBay[0].sale
    }
    else if (this.rateSale[0].ccy != this.rateBay[0].ccy && this.rateSale[0].ccy == "BTC"){
            this.sum1 = (this.sum2 * this.rateSale[0].buy * 39.5 ) / this.rateBay[0].sale
    }
    else if (this.rateSale[0].ccy != this.rateBay[0].ccy  && this.rateBay[0].ccy == "BTC"){
      this.sum1 = (this.sum2 * this.rateSale[0].buy) /(this.rateBay[0].sale * 39.9 )
    }
    else {
      this.sum1 = this.sum2
    }
  }
},
    zeroingInputTwo() {
      this.sum2 = 0
    },
     zeroingInputOne() {
      this.sum1 = 0
    }
 }
 
}

</script>

<style>
* {
	margin: 0;
	padding: 0;
}
body {
background-color: lightgray;	
}
p {
font-size: 20px;
text-align: center;
}
table,th,td {
	margin: 0 auto;
  padding:4px;
	border: 2px solid darkgrey;
}
.main {
	margin: auto;
	padding: 30px;
	color: white;
}
label {
	font-size: 20px;
}
 .inputs {
  width: 700px;
  display:flex;
  justify-content:space-around;
  align-items: center;
  margin-top: 100px;
  margin-left:25%;
 }
 .input {
  width: 200px;
  height: 25px;
  color: white;
  border: 2px solid darkgrey;
  border-radius:2px;
 }
 button {
  margin-left:46%;
 }
 .convert {
  width: 70px;
  height: 50px;
  border: 2px solid darkgrey;
  border-radius:10px;
 }
 .date {
  text-align: center;
  margin-top: 100px;
 }
</style>
