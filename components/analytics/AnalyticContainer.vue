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
    <v-row>
      <v-col align="start" class="pa-10 text-h5" cols="auto">
        <b>Reports Management</b>
      </v-col>
      <v-spacer></v-spacer>
      <v-col align-self="center" align="end" class="pr-10" v-if="account_type!='Staff'">
  
      </v-col>
    </v-row>
    <v-data-table
      class="pa-5"
      :headers="headers"
      :items="events"
      :loading="isLoading"
    >
     <template v-slot:[`item.status`]="{ item }">
        <div>
          <v-chip align="center" :style="getColorStatus(item.status)"
            ><span>{{ item.status }} </span></v-chip
          >
        </div>
      </template>
     <template #[`item.price`]="{ item }">
          <div>
            {{formatPrice(item.price)}}
          </div>
      </template>
      <template v-slot:loading>
        <v-skeleton-loader
          v-for="n in 5"
          :key="n"
          type="list-item-avatar-two-line"
          class="my-2"
        ></v-skeleton-loader>
      </template>

      <template #[`item.opt`]="{ item }">
        <v-menu offset-y z-index="1">
          <template v-slot:activator="{ attrs, on }">
            <v-btn icon v-bind="attrs" v-on="on">
              <v-icon>mdi-dots-horizontal</v-icon>
            </v-btn>
          </template>
          <v-list dense>
            <v-list-item @click.stop="editItem(item)">
              <v-list-item-content>
                <v-list-item-title>Edit</v-list-item-title>
              </v-list-item-content>
            </v-list-item>
            <v-list-item @click.stop="deleteItem(item)">
              <v-list-item-content>
                <v-list-item-title>Delete</v-list-item-title>
              </v-list-item-content>
            </v-list-item>
          </v-list>
        </v-menu>
      </template>
    </v-data-table>
    <div>
         <div class="pie_chart"  style="height:280px" align="center" v-if="chart_data1" >
                    <pie-chart :data="chartData1" :options="chartOptions"></pie-chart>
 </div>
    </div>
  </v-card>
</template>

<script>

import PieChart from "./PieChart.js";
export default {
    components:{PieChart},
  created() {
    this.loadData();
  },
  data() {
    return {
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
      },
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
      chartOptions: {
        chart: {
          title: 'Company Performance',
          subtitle: 'Sales, Expenses, and Profit: 2014-2017',
        },
      },
      buttonLoad:false,
      account_type:'',
      deleteConfirmation:false,
      selectedItem:[],
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