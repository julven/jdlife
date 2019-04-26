<template>
  <div id="app" >
   <b-container class="mt-3">
     <b-row>
     <b-col md=4>
       <b-card>
         <h3>DETAILS</h3>
          <label label-size="sm">Levy Year:</label>
          <b-select v-model="form.val_levy_year"  :options="levy_year" size="sm"></b-select>

          <label label-size="sm">Client Type:</label>
          <b-select v-model="form.val_client_type"  :options="client_type" size="sm"></b-select>

          <label label-size="sm">Employment Status:</label>
          <b-select v-model="form.val_employ_status"  :options="employ_status" size="sm"></b-select>

          <label label-size="sm">Employee Start Date:</label>
          <b-form-input type='date' size="sm"  v-model="form.employ_start"></b-form-input>

          <b-row>
            <b-col xs=12  sm=6 md=12 lg=6>
               <label label-size="sm">Earnings: </label>
              <b-input-group prepend="$"  size="sm">
                <b-form-input type='number' size="sm"  v-model="form.earnings"></b-form-input>
              </b-input-group>
            </b-col>
            <b-col xs=12 sm=6 md=12 lg=6>
              <label label-size="sm">Cover Amount:</label>
                <b-input-group prepend="$"  size="sm">
                <b-form-input  min="29453" type='number' size="sm" v-model="form.cover"></b-form-input>
              </b-input-group>
            </b-col>
          </b-row>
         
          <label label-size="sm">Classification Unit:</label>
          <b-form-input type='number' size="sm"  v-model="form.cu_code"></b-form-input>

          <b-button 
          @click="calculate" 
          variant="outline-primary" 
          block class="mt-3"
          :disabled="loading"
          >Calculate</b-button>

       </b-card>
     </b-col>
     <b-col md=8>
       <b-card>
        <h3>CALCULATIONS</h3>
        <b-table striped hover :items="get_results" :fields="fields"></b-table>
        <div class="text-center" v-show="loading">
          <b-spinner variant="primary" label="Text Centered"></b-spinner>
        </div>
       </b-card>
     </b-col>
     </b-row>
   </b-container>
  </div>
</template>

<script>
export default {
  name: 'app',
    data () {
      return {
        fields: ['type', 'workplace_cover', 'coverPlus_extra_standard', 'coverPlus_extra_lLWC'],
        results: [],
        valid: [null, null, null, null, null, null, null],
        loading: false,
        levy_year: [
          {value:2016, text:2016},
          {value:2017, text:2017},
          {value:2018, text:2018},
          {value:2019, text:2019},
          {value:2020, text:2020},
          {value:2021, text:2021},
        ],
        client_type: [
          {value:'self',text:'self employed'}, 
          {value:'shareholder',text:'shareholder employee'}
        ],
        employ_status: [
          {value:'fullTime',text:'full time'}, 
          {value:'partTime',text:'part time'}
        ],
        form:{
          val_levy_year: 2019,
          val_client_type: 'shareholder',
          val_employ_status: 'fullTime',
          employ_start: new Date('2010-04-01').toISOString().slice(0,10),
          earnings: 100000,
          cover: 29453,
          cu_code: 41110,
        }
      }
    },
    computed: {
      get_results() {
        return this.results;
      },
      
     
    },
    methods: {
       validate (val) {
        return this.valid[val];
      },
      calculate() {
        var err = false;
         Object.keys(this.form).forEach( (val, i) => {
            // console.log(this.form[val])
           if(this.form[val]=="") {
            
             this.valid[i] = false;
             err = true;
           }
         })
        // console.log(this.valid)

        if(err === false){
          this.loading = true;
          this.results = null;
          var data = new FormData();

          Object.keys(this.form).forEach( val => {
            // console.log(val, this.form[val])
            data.append(val, this.form[val])
          });
          
          this.$http.post('https://testjdlife.000webhostapp.com/curl.php',data).then( resp => {
            // console.log(resp.body);
            this.results = resp.body;
            this.loading = false;

          })
          // alert('form ok');
        }else{
          alert('Please fill up all the fields!')
        }
        
      }
    }
  }
</script>

<style>

</style>
