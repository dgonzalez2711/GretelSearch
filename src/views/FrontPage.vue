<template>
  <v-main>
      <v-container>
          <v-row>
            <!-- <v-col cols="12" md="6">
                <h1>2D Representation</h1>
                <Chart v-if="chartLoaded" :chartData = "chartData" :options = "options" />
            </v-col> -->
             <!--<div class="col-md-3" v-for="result in results">!-->
            <div class="panel panel-default">
                <!-- display the city name and country  -->
                    {{ result._source.name }}, {{ result._source.country }}
                </div>
                <div class="panel-body">
                <!-- display the latitude and longitude of the city  -->
                    <p>lat:{{ result._source.lat }}, long: {{ result._source.lng }}.</p>
                </div>
                
            <v-col cols="12" md="6">
                <h1>
                    Research Papers:
                    <v-chip
                        :color="(onlyPeer) ? 'primary lighten-1' : 'primary'"
                    >
                        Total: {{ count }}
                    </v-chip>
                </h1>
                
                <v-switch
                    label="Only Peer Reviewed"
                    v-model="onlyPeer"
                ></v-switch>

                {{ !results.length ? "No results" : "" }}
                
                <div v-if="loading">
                    <v-row v-for="n in 3" :key="n" class="mb-4">
                        <v-col
                            cols="12" md="10"
                        >
                            <v-skeleton-loader
                                type="card"
                            ></v-skeleton-loader>
                        </v-col>
                    </v-row>
                </div>
                
     
            
                <v-list v-else v-for="result in results" :key="result._id">
                    <PaperCard 
                        v-if="checkPeer(result._source.peer_reviewed)" 
                        :title="result._source.title" 
                        :content="result._source.excerpt" 
                        :author ="result._source.authors" 
                        :url="result._source.url"
                        :score="result._source.score"
                    />
                </v-list>
            </v-col>
          </v-row>
          
      </v-container>
  </v-main>
  
</template>
<style>
    .search-form .form-group {
        float: right !important;
        transition: all 0.35s, border-radius 0s;
        width: 500px;
        height: 32px;
        background-color: #fff;
        box-shadow: 0 1px 1px rgba(0, 0, 0, 0.075) inset;
        border-radius: 25px;
        border: 1px solid #ccc;
        
    }
  
    .search-form .form-group input.form-control {
        padding-right: 20px;
        border: 0 none;
        background: transparent;
        box-shadow: none;
        display: block;
        
    }

    .search-form .form-group input.form-control::-webkit-input-placeholder {
        display: none;
    }

    .search-form .form-group input.form-control:-moz-placeholder {
        /* Firefox 18- */
        display: none;
    }

    .search-form .form-group input.form-control::-moz-placeholder {
        /* Firefox 19+ */
        display: none;
    }

    .search-form .form-group input.form-control:-ms-input-placeholder {
        display: none;
    }

    

    .search-form .form-group span.form-control-feedback {
        position: absolute;
        top: -1px;
        right: -2px;
        z-index: 2;
        display: block;
        width: 34px;
        height: 34px;
        line-height: 34px;
        text-align: center;
        color: #3596e0;
        left: initial;
        font-size: 14px;
    }
</style>
<script>
import PaperCard from '../components/PaperCard'
// import Chart from './Chart'

export default {
    name: 'FrontPage',
    components:{
        PaperCard
        // Chart
    },
    props:{
        results: Array
    },
    data: () => ({
        onlyPeer: false,
        chartLoaded: true
    }),
    computed:{
        count(){
            if(!this.onlyPeer) return this.results.length
            else return this.peerReviewedPaperCount
        },
        loading(){
            return this.$store.state.loading
        },
        peerReviewedPaperCount(){
            let num = 0
            this.results.forEach((result) => {
                if(result._source.peer_reviewed == 'True') num++
            })
            return num
        },
        chartData(){
            return {
                datasets:[{
                    label: 'Covid19 Research Papers Scatter Dataset',
                    data: [
                        {
                            x: -10,
                            y: 0
                        }, 
                        {
                            x: 0,
                            y: 10
                        }, 
                        {
                            x: 10,
                            y: 5
                        }
                    ]
                }]
            }
        },
        options(){
            return {
                scales: {
                    xAxes: [{
                        type: 'linear',
                        position: 'bottom'
                    }]
                }
            }
        }
    },
    methods:{
        checkPeer(status){
            if(this.onlyPeer == false) return true
            else if(this.onlyPeer == true && status == 'True') return true
            else return false
        }
    },
    mounted(){
        console.log("Front Page Mounted")
    }
}
</script>
<script>

    // create a new Vue instance
var app = new Vue({
    el: '#app',
    // declare the data for the component (An array that houses the results and a query that holds the current search string)
    data: {
        results: [],
        query: ''
    },
    // declare methods in this Vue component. here only one method which performs the search is defined
    methods: {
        // make an axios request to the server with the current search query
        search: function() {
            axios.get("http://127.0.0.1:3001/search?q=" + this.query)
                .then(response => {
                    this.results = response.data;

                })
        }
    },
    // declare Vue watchers
    watch: {
        // watch for change in the query string and recall the search method
        query: function() {
            this.search();
        }
    }

})
</script>

<style>

</style>