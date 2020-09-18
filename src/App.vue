<template>
  <v-app>

      <v-app-bar
        app
        dark
      >
        <v-app-bar-nav-icon></v-app-bar-nav-icon>
        <v-toolbar-title> Gretel Search </v-toolbar-title>

        <v-spacer></v-spacer>

        <v-container style="margin-top: 1.5em; float: right">
            <v-row>
                <v-col
                    cols="8"
                    md="6"
                >
                    <v-text-field
                        dense
                        autofocus
                        dark
                        filled=""
                        v-model="searchString"
                        placeholder="Search"
                    ></v-text-field>
                </v-col>
                <v-col
                    cols="2"
                >
                    <v-btn @click="getData()" icon>
                        <v-icon>mdi-magnify</v-icon>
                    </v-btn>
                </v-col>
            </v-row>
        </v-container>

      </v-app-bar>

    <FrontPage :results = "results" />

  </v-app>
</template>

<script>
import axios from 'axios'

import FrontPage from './views/FrontPage'

export default {
    name: 'App',
    components:{
        FrontPage
    },
    data: () => ({
        searchString: "",
        results: []
    }),
    methods:{
        
        getData(){
            // this.$store.state.loading = true
            this.$store.dispatch('setLoading')

            let url = this.$store.state.getURL

            //Replace spaces in searchString with plus sign (+)
            this.searchString = this.searchString.replace(" ", "+")
            
            let finalURL = url + this.searchString
            
            console.log(finalURL)

            //Axios Get Request
            axios.get(finalURL)
            .then((response) => {
                this.results = response.data.data.hits.hits
                console.log(response)
                this.$store.dispatch('setNotLoading')
            })

        }
    }
}
</script>

<style>

</style>
<!-- The core Firebase JS SDK is always required and must be listed first -->
<script src="/__/firebase/7.21.0/firebase-app.js"></script>

<!-- TODO: Add SDKs for Firebase products that you want to use
     https://firebase.google.com/docs/web/setup#available-libraries -->
<script src="/__/firebase/7.21.0/firebase-analytics.js"></script>

<!-- Initialize Firebase -->
<script src="/__/firebase/init.js"></script>