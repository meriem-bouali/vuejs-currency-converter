<template>
  <div class="container my-5">
    <div class="row">
      <div class="col-md-6">
        <label>From</label>
        <select v-model="from"  class="form-select">
          <option v-for="(currency, index) in formattedCurrencies" :key="index">{{currency.id}}</option>
        </select>
      </div>
      <div class="col-md-6">
        <label>To</label>
        <select v-model="to"  class="form-select" >
          <option  v-for="(currency, index) in formattedCurrencies" :key="index">{{currency.id}}</option>
        </select>
      </div>
    </div>
    <div class="row">
      <div class="col-md-6 offset-md-3">
        <input v-model="amount" type="text" placeholder="Amount" class="form-control my-5" />
        <div class="text-center">
          <button class="btn btn-lg btn-primary" @click="covertCurency()" :disabled="disabled">
            {{loading? 'Coverting ...': 'Convert'}}
          </button>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-md-6 offset-md-3">
        <h1 class="text-center display-2 mt-5">{{calculateResult}}</h1>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      api: "https://free.currconv.com/api/v7/",
      apiKey:"apiKey=a239cd4f991094993b82",
      currencies: {},
      from: "",
      to: "",
      amount: "",
      results: "",
      loading: false,
    };
  },
  mounted() {
    this.getCurencies();
  },
  watch:{  // watch the property
    from(){
      this.results=""
    },
    to(){
      this.results=""
    }
  },
  computed:{ // refresh her self when the data in changed
    formattedCurrencies(){
      return Object.values(this.currencies)
    },
    calculateResult(){

      return ( this.amount==="" || this.results==="") ? "": (Number(this.amount)*this.results).toFixed(3);
    },

    disabled(){
      return this.amount==="" || this.to==="" || this.from==="" || this.loading
    }
  },
  methods: {
    getCurencies() {
      const currencies = localStorage.getItem("currencies");
      if (currencies) {
        this.currencies = JSON.parse(currencies);
        return;
      }
      // https://free.currconv.com/api/v7/currencies?apiKey=a239cd4f991094993b82
      //${this.api}currencies?${this.apiKey}
      this.axios.get(this.api+"currencies?"+this.apiKey).then((response) => {
        this.currencies = response.data.results;
        localStorage.setItem(
          "currencies",
          JSON.stringify(response.data.results)
        );
      });
    },
    covertCurency(){
      this.loading=true;
      this.axios.get(this.api+"convert?q="+this.from+"_"+this.to+"&compact=ultra&"+this.apiKey).then((response)=>{
        console.log(response)
        this.results=response.data[this.from+"_"+this.to]
        this.loading=false;
      }).catch((e)=>{
        console.log(e.toString())
          this.loading=false;
      });

    }
  },
};
</script>


<style>
</style>