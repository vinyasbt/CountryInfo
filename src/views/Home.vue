
<template>
  <div class="home">
    <vue-pull-refresh :on-refresh="onRefresh"></vue-pull-refresh>
    <!-- <img alt="Vue logo" src="../assets/logo.png" />
    <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <div>
      <b-navbar toggleable="lg" type="dark" variant="danger">
         
        <b-navbar-nav>
          <b-navbar-brand href="/">RestCountries</b-navbar-brand>
          <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>
        </b-navbar-nav>
         <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav class="ml-auto">
          <b-nav-form>
            <label>Search type</label>
            <select v-model="type">
              <option value="FULLNAME" selected>FULL NAME</option>
              <option value="CURRENCY">CURRENCY</option>
              <option value="LANGUAGE">LANGUAGE</option>
              <option value="CAPITALCITY">CAPITAL CITY</option>
              <option value="CALLINGCODE">CALLING CODE</option>
              <option value="REGION">REGION</option>
            </select>
            
            <b-form-input
              size="sm"
              class="mr-sm-2"
              placeholder="Search"
              v-model="name"
            >
            </b-form-input>
            <ul class="list-group" v-if="type=='FULLNAME'">
            <div v-for="(country, index) in watcherCountries" :key="index" >
         <button type="button" class="list-group-item list-group-item-action" @click="searchName(country.name)">{{country.name}}</button>
      </div>
            </ul>
            <b-button size="sm" @click="searchName()">Search</b-button>
          </b-nav-form> 
        </b-navbar-nav>
         </b-collapse>
      </b-navbar>
      
    </div>
    <b-container fluid>
      <b-row>
          <div v-if="particularCountry.length != 0">
            <b-col
              v-for="(country, index) in particularCountry"
              :key="index"
              class="box"
            >
              <h3 class="h3-responsive">Name:{{ country.name }}</h3>
              <h5 class="h5-responsive">Region:{{ country.region }}</h5>
              <h5 class="h5-responsive">Capital:{{ country.capital }}</h5>
              <h5 class="h5-responsive">subregion:{{ country.subregion }}</h5>
              <h5 class="h5-responsive">population:{{ country.population }}</h5>
              <h5 class="h5-responsive">area:{{ country.area }}</h5>
              <h5 class="h5-responsive">nativeName:{{ country.nativeName }}</h5>
              <h5 class="h5-responsive">callingCodes:{{ country.callingCodes.join(",") }}</h5>
              <h5 class="h5-responsive">timezones:{{ country.timezones.join(",") }}</h5>
              <h5 class="h5-responsive">currency:{{ country.currencies[0].name }}</h5>
              <h5 class="h5-responsive">currency symbol:{{ country.currencies[0].symbol }}</h5>
              <h5 class="h5-responsive">borders:{{ country.borders.join(",") }}</h5>
            </b-col>
          </div>
          <div v-else>
            <h3>List of Countries</h3>
            <div
              v-for="(country, index) in allCountries"
              :key="index"
              class="grid"
            >
              <div @click="searchName(country.name)">
                <h3>Name:{{ country.name }}</h3>
                <h5>Region:{{ country.region }}</h5>
              </div>
            </div>
          </div>
      </b-row>
    </b-container>
    
  </div>
</template>

<script>
import axios from "axios";
import VuePullRefresh from 'vue-pull-refresh';
export default {
  name: "Home",
  data() {
    return {
      allCountries: [],
      name: "",
      particularCountry: [],
      watcherCountries: [],
      type: "",
    };
  },
  components: {
        'vue-pull-refresh': VuePullRefresh
    },
  created() {
    axios.get("https://restcountries.eu/rest/v2/all").then((res) => {
      this.allCountries = res.data;
    });
  },
  methods: {
    searchName(value) {
      console.log("in type", this.type);
      console.log("in name", this.name);
      console.log("value in method", value);
      if (this.type == "CURRENCY") {
        axios
          .get(`https://restcountries.eu/rest/v2/currency/${this.name}`)
          .then((res) => {
            this.particularCountry = res.data;
            console.log(this.particularCountry);
          });
      } else if (this.type == "LANGUAGE") {
        axios
          .get(`https://restcountries.eu/rest/v2/lang/${this.name}`)
          .then((res) => {
            this.particularCountry = res.data;
            console.log(this.particularCountry);
          });
      } else if (this.type == "CAPITALCITY") {
        axios
          .get(`https://restcountries.eu/rest/v2/capital/${this.name}`)
          .then((res) => {
            this.particularCountry = res.data;
            console.log(this.particularCountry);
          });
      } else if (this.type == "CALLINGCODE") {
        axios
          .get(`https://restcountries.eu/rest/v2/callingcode/${this.name}`)
          .then((res) => {
            this.particularCountry = res.data;
            console.log(this.particularCountry);
          });
      } else if (this.type == "REGION") {
        axios
          .get(`https://restcountries.eu/rest/v2/region/${this.name}`)
          .then((res) => {
            this.particularCountry = res.data;
            console.log(this.particularCountry);
          });
      } else if (value == undefined) {
        axios
          .get(
            `https://restcountries.eu/rest/v2/name/${this.name}?fullText=true`
          )
          .then((res) => {
            this.particularCountry = res.data;
            console.log(this.particularCountry);
          });
      } else if (this.type == "FULLNAME" || value!=undefined) {
        axios
          .get(`https://restcountries.eu/rest/v2/name/${value}?fullText=true`)
          .then((res) => {
            this.particularCountry = res.data;
            console.log(this.particularCountry);
            this.watcherCountries.length=0;
          });
      }
    },
    onRefresh: function() {
            return new Promise(function (resolve) {
                setTimeout(function () {
                    resolve();
                }, 1000);
            });
        }
  },
  watch: {
    name() {
      if(this.name.length>0){
      axios
        .get(`https://restcountries.eu/rest/v2/name/${this.name}`)
        .then((res) => {
          this.watcherCountries = res.data;
          console.log(this.watcherCountries);
        });
      }
      else{
        this.watcherCountries.length=0;
      }
    },
  },
};
</script>
<style >
.grid {
  width: 150px;
  height: 150px;
  margin: 50px;
  float: left;
}
.box {
  width: 350px;
  height: 550px;
  margin: 30px;
  float: left;
}
</style>
