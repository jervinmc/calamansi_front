<template>
  <v-card elevation="5">
     <v-dialog v-model="deleteConfirmation" width="500" persistent>
    <v-card class="pa-10">
    <div align="center" class="text-h6">Confirmation</div>
    <div align="center" class="pa-10">
        Are you sure you want to delete this item?
    </div>
      <v-card-actions>
        <v-row align="center">
            <v-col align="end">
                <v-btn color="red" text @click="deleteConfirmation=false"> Cancel </v-btn>
            </v-col>
            <v-col>
                <v-btn color="success" text :loading="buttonLoad" @click="deleteValue"> Confirm </v-btn>
            </v-col>
        </v-row>
      </v-card-actions>
    </v-card>
  </v-dialog>
    <div>
         <!-- <div class="pie_chart"  style="height:280px" align="center" v-if="chart_data1" >
                    <pie-chart :data="chartData1" :options="chartOptions"></pie-chart>
 </div> -->
    <div class="pl-5 text-h5">
     <b> Monthly Report </b>
    </div>
  <div v-if="isGraph">
     <VueApexCharts width="1000" type="bar" :options="chartOptions" :series="series"></VueApexCharts>
   </div>
    </div>
  </v-card>
</template>

<script>

import PieChart from "./PieChart.js";
import { Bar } from 'vue-chartjs'
import VueApexCharts from 'vue-apexcharts'

export default { 
    components:{PieChart,Bar,VueApexCharts},
  created() {
    this.loadData();
  },
  data() {
    return {
      chartData5: {
        labels: [ 'January', 'February', 'March'],
        datasets: [
          {
            label: 'Data One',
            backgroundColor: '#f87979',
            data: [40, 20, 12]
          }
        ]
      },
       chartData2: {
        labels: [ 'January', 'February', 'March'],
        datasets: [
          {
            label: 'Data One',
            backgroundColor: '#f87979',
            data: [40, 20, 12]
          }
        ]
      },
      chartOptions2: {
        responsive: true
      },
    chartData1: {
        responsive:false,
        hoverBackgroundColor: "red",
        hoverBorderWidth: 10,
        labels: ['Greasy Spot','Greening','Phytphthora','Sooty Mold','Cranker','Citrus Scab','Alternaria Brown Spot'],
        datasets: [
          {
            label: "Data One",
            backgroundColor: ['#E3C790', '#344557'],
            data: [0,0,0,0,0,0,0]
          }
        ]
      },chart_data1:false,
        chartData: {
        responsive:false,
        hoverBackgroundColor: "red",
        hoverBorderWidth: 10,
        chart_data1:false,
        labels: [],
        datasets: [
          {
            label: "Data One",
            backgroundColor: ['#E3C790', '#344557'],
            data: []
          }
        ]
      },
      chartOptions_pie: {
        chart: {
          title: 'Company Performance',
          subtitle: 'Sales, Expenses, and Profit: 2014-2017',
        },
      },
       chartOptions: {
          chart: {
            id: 'vuechart-example'
          },
          xaxis: {
            categories: ["January", "February", "March", "April", "May", "June", "July", "August","September","October","November","December"]
          }
        },
        series: [{
          name: 'series-1',
          data: [30, 40, 35, 50, 49, 60, 70, 91,19,19,29,10]
        }],
      buttonLoad:false,
      account_type:'',
      deleteConfirmation:false,
      selectedItem:[],
      isGraph:false,
        events:[],
      selectedItem:{},
      isLoading: false,
      users: [],
      dialogAdd:false,
      isAdd:true,
      headers: [
        { text: "ID", value: "id" },
        { text: "Diseases", value: "disease" },
        { text: "Quantity", value: "quantity" },
        ,
      ],
    };
  },
  methods: {
     getColorStatus(item) {
    if (item == "Pending") {
        return "background-color:#FFF5CC;border-radius:15px;padding:7px; width:150px; color: #344557;";
      }
      else if(item =='Approved'){
          return "background-color:green;border-radius:15px;padding:7px; width:150px; color:white;";
      } else  { 
        return "background-color:red;border-radius:15px;padding:7px; width:150px; color: white;";
      } 
      
    },
    async deleteValue(){
     this.buttonLoad=true
      this.$axios.delete(`/beneficiaries/${this.selectedItem.id}/`,{
        headers:{
          Authorization:`Bearer ${localStorage.getItem('token')}`
        }
      })
      .then(()=>{
          this.deleteConfirmation=false
          this.buttonLoad=false
          alert('Successfully Deleted!')
          this.loadData()
      })
    },
     deleteItem(val){
      this.selectedItem = val
      this.deleteConfirmation=true
    },

     formatPrice(value) {
      let val = (value / 1).toFixed(2).replace(",", ".");
      return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
    editItem(val){
      this.selectedItem=val
      this.dialogAdd=true
      this.isAdd=false
    },
    addItem(){
      this.isAdd=true
      this.dialogAdd=true
    },
    async status(data, status) {
      this.isLoading = true;
      const res = await this.$axios
        .patch(
          `/announcement/${data.id}/`,
          {
            is_active: status == "Deactivate" ? false : true,
          },
          {
            headers: {
              Authorization: `Bearer ${localStorage.getItem("token")}`,
            },
          }
        )
        .then((res) => {
          this.loadData();
        });
    },
    loadData() {
      this.account_type=localStorage.getItem('account_type')
      this.eventsGetall();
    },
    async eventsGetall() {
      this.isLoading = true;
      const res = await this.$axios
        .get(`/logs`, {

        })
        .then((res) => {
          this.series[0]['data'][0] = res.data.filter(data=>new Date(data.date).getMonth()==0).length
         this.series[0]['data'][1] = res.data.filter(data=>new Date(data.date).getMonth()==1).length
         this.series[0]['data'][2] = res.data.filter(data=>new Date(data.date).getMonth()==2).length
         this.series[0]['data'][3] = res.data.filter(data=>new Date(data.date).getMonth()==3).length
         this.series[0]['data'][4] = res.data.filter(data=>new Date(data.date).getMonth()==4).length
         this.series[0]['data'][5] = res.data.filter(data=>new Date(data.date).getMonth()==5).length
         this.series[0]['data'][6] = res.data.filter(data=>new Date(data.date).getMonth()==6).length
         this.series[0]['data'][7] = res.data.filter(data=>new Date(data.date).getMonth()==7).length
         this.series[0]['data'][8] = res.data.filter(data=>new Date(data.date).getMonth()==8).length
         this.series[0]['data'][9] = res.data.filter(data=>new Date(data.date).getMonth()==9).length
         this.series[0]['data'][10] = res.data.filter(data=>new Date(data.date).getMonth()==10).length
         this.series[0]['data'][11] = res.data.filter(data=>new Date(data.date).getMonth()==11).length
         this.isGraph = true
         console.log(this.series)
          console.log(res.data);
          this.events = res.data;
           var greasy =  this.events.filter(item => item.disease=='Greasy Spot')
          if(greasy.length>0){
               this.chartData1.datasets[0].data[0]=greasy[0].quantity
          }
          var greening =  this.events.filter(item => item.disease=='Greening')
          if(greening.length>0){
               this.chartData1.datasets[0].data[1]=greening[0].quantity
          }
         
          var greening =  this.events.filter(item => item.disease=='Phytphthora')
         
          if(greening.length>0){
               this.chartData1.datasets[0].data[2]=greening[0].quantity
          }
          var greening =  this.events.filter(item => item.disease=='Sooty Mold')
           if(greening.length>0){
               this.chartData1.datasets[0].data[3]=greening[0].quantity
          }
          var greening =  this.events.filter(item => item.disease=='Cranker')
          if(greening.length>0){
               this.chartData1.datasets[0].data[4]=greening[0].quantity
          }
          var greening =  this.events.filter(item => item.disease=='Citrus Scab')
         if(greening.length>0){
               this.chartData1.datasets[0].data[5]=greening[0].quantity
          }
          var greening =  this.events.filter(item => item.disease=='Alternaria Brown Spot')
         if(greening.length>0){
               this.chartData1.datasets[0].data[6]=greening[0].quantity
          }
          this.isLoading = false;
          this.chart_data1=true;
        });
    },
  },
};
</script>

<style>
.pie_chart {
   margin: 0px 0px 30px 20px;
    max-width: 250px;
  }
</style>