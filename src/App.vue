<template>
  <div id="app">
    <!--title-->
    <h1 class="site-title">{{title}}</h1>
    <span class="site-description">{{ currentDate }}</span>  

    <!--entry list-->
    <ul class="entry-list">
      <li 
        v-for="entry in filteredEntries"
        :key="entry.id"
        class="entry-item"
        >
        <span class="entry-daytime">{{ entry [0] }} Uhr,{{ entry[1].replaceAll("/", "/" )}} </span>
        <h3 class="entry-title">Basisbesuch BB</h3>
        <span class="entry-description">Interessenten besuchen uns</span>
      </li>
    </ul>

    <!--footer-->
    <footer class="footer">
      <img src="./assets/STZH_SEB_Logo.png" alt="Stadt Zuerich Soziale Betriebe und Einrichtungen Logo">
      <img src="./assets/Opportunity.png" alt="Opportunity Logo">
      <img src="./assets/SAG_Logo_De.png" alt="Stiftung Arbeitsgestaltung Logo">
    </footer>

  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      title: "Welcome to Opportunity",
      currentDate:"", 
      gsheet_url: "https://sheets.googleapis.com/v4/spreadsheets/1qLZJwuNv3QmwGhSj1wZZbuXNOkDKN-Ha7fo0Ca_uVVU/values:batchGet?ranges=A1%3AE100&valueRenderOption=FORMATTED_VALUE&key=AIzaSyBesotaNgSaTUIhrSKjEaExdi-ksKInhoE",
      entries: [],
    };
  },
  computed: { // computed properties are like data properties, but with a method combined, it gets executed automatically, instead of calling a function explicitly
    filteredEntries() {
      return [...this.entries].slice(1); //remove first item of array
    },

  },
  methods: {
    getData() {
      axios.get(this.gsheet_url).then((response) => {
        this.entries = response.data.valueRanges[0].values;       
      });     
    },

    updateCurrentDate() {
      let today = new Date();
      const date = `${today.getDate()}.${today.getMonth() + 1}.${today.getFullYear()}`;
      this.currentDate = date;           
    },

    refreshData(){
      this.getData();
      this.updateCurrentDate();      
    }   
  },

    mounted() {
      this.refreshData(); //get first initial data and wait for next update
      setInterval(() => {
        this.refreshData();
      }, 1800000) //wait 30min for next update..milliseconds
  }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Inter:wght@500;900&display=swap%22");

body {
  background: #e8eff4;
}

#app {
  font-family: "Inter", Helvetica, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 60px;
  color: #323d4a;
}

.site-title {
  font-size: 62px;
  font-weight: 900;
  margin: 80px 0 20px 0;
}

.site-description {
  font-weight: 500;
  font-size: 62px;
  color: #9aa7b1;
  margin: 0;
}

.entry-list {
  padding-left: 0;
}

.entry-item {
  padding: 35px 40px;
  margin: 40px 0;
  font-size: 28px;
  line-height: 1.3;
  list-style: none;
  background: #0f05a0;
}

.entry-daytime {
  font-weight: 900;
  color: #eb5e00;
}

.entry-title {
  font-size: inherit;
  font-weight: 900;
  color: #ffbfab;
  margin: 0;
}

.entry-description {
  font-weight: 500;
  color: #ffbfab;
}

.footer {
  display: flex;  
  justify-content: space-between;
  padding: 40px;
  box-sizing: border-box;
  background: #ffff;
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;   
}

.footer img {
  height: 50px;
}

</style>