<template>
  <v-app>
    <v-card
      height="100%"
      width="100%"
    >
      <v-container> 
        <v-row>
          <v-col cols=12>        
            <v-alert
              v-show="alert"
              elevation="12"
              outlined
              prominent
              shaped
              text
              type="error"
            >Please input minimum 3 symbols!</v-alert>
          </v-col>        
          <v-col cols=12>
            <v-app-bar
              color="blue darken-3 "
              dark
            >
              <v-col cols=2>
                <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
              </v-col>
              <v-col cols=8>
                <v-text-field
                  label="Search by name"
                  hide-details="auto"
                  placeholder="Input Beer name and press Enter"
                  v-model="searchQuery"
                  @keyup.enter="search()"
                ></v-text-field>       
              </v-col>
              <v-col cols=2 class="d-flex align-center">
                <v-btn
                  width="100%"
                  depressed
                  color="primary"
                  @click="search()"
                >
                  Enter
                </v-btn>
              </v-col>          
            </v-app-bar>
            <v-navigation-drawer
              v-model="drawer"
              absolute
              temporary
              width=350
            >
            <div height=500>          
              <v-container >
                <v-row class="ma-4">
                  <v-col cols=12>
                    <h3>Filters</h3>
                  </v-col>
                  <v-col cols=12>
                    <div>Abv</div>
                  </v-col>
                  <v-col>
                    <v-text-field
                      class="pa-0 ma-0"
                      label="Greater than"
                      hide-details="auto"
                      v-model="filters[0].value"
                    ></v-text-field>
                  </v-col>
                  <v-col>
                    <v-text-field
                      class="pa-0 ma-0"
                      label="Less than"
                      hide-details="auto"
                      v-model="filters[1].value"                      
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-row class="ma-4">
                  <v-col cols=12>
                    <div>Ibu</div>
                  </v-col>
                  <v-col>
                    <v-text-field
                      class="pa-0 ma-0"
                      label="Greater than"
                      hide-details="auto"
                    ></v-text-field>
                  </v-col>
                  <v-col>
                    <v-text-field
                      class="pa-0 ma-0"
                      label="Less than"
                      hide-details="auto"
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-row class="ma-4">
                  <v-col cols=12>
                    <div>Ebc</div>
                  </v-col>
                  <v-col>
                    <v-text-field
                      class="pa-0 ma-0"
                      label="Greater than"
                      hide-details="auto"
                    ></v-text-field>
                  </v-col>
                  <v-col>
                    <v-text-field
                      class="pa-0 ma-0"
                      label="Less than"
                      hide-details="auto"
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-row class="ma-4">
                  <v-col cols=12>
                    <div>Brewed</div>
                  </v-col>
                  <v-col>
                    <v-menu
                      ref="brewedAfter"
                      v-model="brewedAfter"
                      :close-on-content-click="false"
                      :return-value.sync="date"
                      transition="scale-transition"
                      offset-y
                      max-width="290px"
                      min-width="auto"
                    >
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field
                          v-model="filters[6].value"
                          label="After"
                          prepend-icon="mdi-calendar"
                          readonly
                          v-bind="attrs"
                          v-on="on"
                        ></v-text-field>
                      </template>
                      <v-date-picker
                        v-model="filters[6].value"
                        type="month"
                        no-title
                        scrollable
                      >
                        <v-spacer></v-spacer>
                        <v-btn
                          text
                          color="primary"
                          @click="brewedAfter = false"
                        >
                          Cancel
                        </v-btn>
                        <v-btn
                          text
                          color="primary"
                          @click="$refs.brewedAfter.save(date)"
                        >
                          OK
                        </v-btn>
                      </v-date-picker>
                    </v-menu>
                  </v-col>
                  <v-col>
                    <v-menu
                      ref="brewedBefore"
                      v-model="brewedBefore"
                      :close-on-content-click="false"
                      :return-value.sync="date"
                      transition="scale-transition"
                      offset-y
                      max-width="290px"
                      min-width="auto"
                    >
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field
                          v-model="filters[7].value"
                          label="Before"
                          prepend-icon="mdi-calendar"
                          readonly
                          v-bind="attrs"
                          v-on="on"
                        ></v-text-field>
                      </template>
                      <v-date-picker
                        v-model="filters[7].value"
                        type="month"
                        no-title
                        scrollable
                      >
                        <v-spacer></v-spacer>
                        <v-btn
                          text
                          color="primary"
                          @click="brewedBefore = false"
                        >
                          Cancel
                        </v-btn>
                        <v-btn
                          text
                          color="primary"
                          @click="$refs.brewedBefore.save(date)"
                        >
                          OK
                        </v-btn>
                      </v-date-picker>
                    </v-menu>
                  </v-col>
                  <v-col cols="12">
                    <v-btn
                      width="100%"
                      depressed
                      color="primary"
                      @click="filter(), drawer = false"
                    >
                      Filter
                    </v-btn>
                  </v-col>                  
                </v-row>                
              </v-container>
            </div>             
            </v-navigation-drawer>
          </v-col>        
        </v-row>     
        <v-row>
          <v-col cols=3 v-for="(beer, index) in this.beers" :key="index">
            <v-card height="100%">
              <v-card-title>{{ beer.name }}</v-card-title>
              <v-img :src="beer.image_url ? beer.image_url : altImgSrc"  max-height="150" max-width="250" contain></v-img>
            </v-card>
          </v-col>
          <v-col v-if="countIsZero">
            <v-alert
              elevation="12"
              outlined
              prominent
              shaped
              text
              type="warning"
            >We could not find any items with given search query...</v-alert>
          </v-col>
          <v-col v-if="lastPageBeerCount >= 20" cols=12 class="d-flex justify-center">
            <v-btn
              width="150px"
              depressed
              color="primary"
              @click="loadMore()"
            >
              Load more
            </v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-card>
  </v-app>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',

  data: function(){
    return {
      beers: [],
      lastPageBeerCount: 20,
      rootApiUrl: 'https://api.punkapi.com/v2/beers?per_page=20&',
      currentApiUrl: 'https://api.punkapi.com/v2/beers?per_page=20&', 
      altImgSrc: 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/ac/No_image_available.svg/2048px-No_image_available.svg.png',
      searchQuery: '',
      drawer: false,
      date: new Date().toISOString().substr(0, 7),
      brewedAfter: false,
      brewedBefore: false,
      alert: false,
      countIsZero: false,
      page: 2,
      filters: [
        {
          title: 'abv_gt',
          value: null
        },
        {
          title: 'abv_lt',
          value: null
        },
        {
          title: 'ibu_gt',
          value: null
        },
        {
          title: 'ibu_lt',
          value: null
        },
        {
          title: 'ebc_gt',
          value: null
        },
        {
          title: 'ebc_lt',
          value: null
        },
        {
          title: 'brewed_after',
          value: ''
        },
        {
          title: 'brewed_before',
          value: null
        },
      ]
    }
  },
  mounted: function(){
    this.getBeers();
  },
  methods: {
    getBeers: async function(){
     await axios.get(this.rootApiUrl).then( resp => this.beers = resp.data)
     console.log(this.beers);
    },
    search: async function(){
      if(this.searchQuery.length >= 3){
        this.currentApiUrl = `${this.rootApiUrl}beer_name=${this.searchQuery}&`; 
        await axios.get(this.currentApiUrl).then( resp => this.beers = resp.data)
        if(this.beers.length == 0) this.countIsZero = true;
        this.page = 2;
        this.lastPageBeerCount = this.beers.length;
      }else{
        this.alertMessage();
      }
    },
    filter: async function(){
      let filterQuery = '';
      this.filters.forEach(el => {
        let filterValueLength = el.value ? el.value.length : 0;
        if(filterValueLength > 0){
          let filterValue = el.value.replaceAll(" ", "_");
          if(filterValue.includes("-")){
            filterValue = this.formatDate(filterValue);
          }
          filterQuery += `${el.title}=${filterValue}&`
        }
      })
      this.currentApiUrl = `${this.rootApiUrl}${filterQuery}`; 
      await axios.get(this.currentApiUrl).then(resp => this.beers = resp.data);
      this.page = 2;
    },
    formatDate: function(date){
      const[year, month] = date.split('-');
      return `${month}-${year}`;
    },
    alertMessage: function(){
      this.alert = true;
      setTimeout(() => this.alert = false, 1500)
    },
    loadMore: async function(){
      await axios.get(`${this.currentApiUrl}page=${this.page}`).then( resp => {
        resp.data.forEach(el => {
          this.beers.push(el)
        })
        this.page++
        this.lastPageBeerCount = resp.data.length
        console.log(this.lastPageBeerCount)
      })
    }
  },
};
</script>
