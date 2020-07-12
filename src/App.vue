<template>
  <div id="app">
     <div>
      <b-navbar type="light" variant="light">
        <b-nav-form>
          <h3>Accordous Test</h3>
        </b-nav-form>
      </b-navbar>
    </div>
    <div class="container">
      <div class="row mt-2">
        <div class="col-6">
             <b-button id="show-btn" @click="showModal">Add Immobile</b-button>
        </div>
        <div class="col-12">
            <div class="mt-5">
              <b-table striped hover :items="items" :fields="fields"></b-table>
            </div>
        </div>
      </div>
    </div>
   
    <b-modal size="xl" ref="my-modal" hide-footer title="Informations">
        <div class="container">
          <div class="row">
              <h4>Store Immobile</h4>

              <div class="col-12">
                <div class="form-group">
                  <label for="street"><span class="text-danger">*</span>Street</label>
                  <input type="text" class="form-control" id="street" name="street" v-model="street" maxlength="255">
                </div>
              </div>

              <div class="col-2">
                <div class="form-group">
                  <label for="street"><span class="text-danger">*</span>Number</label>
                  <input type="text" class="form-control" id="number" name="number" v-model="number">
                </div>
              </div>

              <div class="col-10">
                <div class="form-group">
                  <label for="street">Complement</label>
                  <input type="text" class="form-control" id="complement" name="complement" v-model="complement" maxlength="255">
                </div>
              </div>

              <div class="col-12">
                <div class="form-group">
                  <label for="street"><span class="text-danger">*</span>Neighborhood</label>
                  <input type="text" class="form-control" id="neighborhood" name="neighborhood" v-model="neighborhood" maxlength="255">
                </div>
              </div>

              <div class="col-10">
                <div class="form-group">
                  <label for="street"><span class="text-danger">*</span>City</label>
                  <input type="text" class="form-control" id="city" name="city" v-model="city" maxlength="255">
                </div>
              </div>

              <div class="col-2">
                <div class="form-group">
                  <label for="street"><span class="text-danger">*</span>State</label>
                  <input type="text" class="form-control" id="state" name="state" v-model="state" maxlength="2">
                </div>
              </div>

              <div class="col-12">
                <div class="form-group">
                  <label for="street"><span class="text-danger">*</span>E-mail Contact</label>
                  <input type="text" class="form-control" id="email_contact" name="email_contact" v-model="email_contact" maxlength="255">
                </div>
              </div>

              <div class="col-12">
              <input @click="sotoreImmobile()" type="submit" class="btn btn-primary" value="Submit" />
              
              </div>
              <small class="text-danger mt-2">* Required fields</small>

          </div>
        </div>
    </b-modal>
  </div>
</template>

<script>

import Vue from 'vue';
import { BootstrapVue } from 'bootstrap-vue'
import VueSweetalert2 from 'vue-sweetalert2'
import axios from 'axios'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
window.Vue = Vue
Vue.use(BootstrapVue)
Vue.use(VueSweetalert2)
axios.defaults.baseURL = 'http://localhost:8088/api/v1/'

export default {

   data() {
      return {
        street: '',
        number: '',
        complement: '',
        neighborhood:'',
        city:'',
        state:'',
        email_contact:'',
        reg_number:/^[0-9]*$/,
        reg: /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,24}))$/,
        error: false,
        fields: [
          {
            key: 'email_contact',
            label: 'E-mail contact',
            sortable: true
          },
          {
            key: 'address',
            label: 'Address',
            sortable: true
          },
          {
            key: 'status',
            label: 'Status',
            sortable: true
          }
        ],
        items: []
      }
    },
  name: 'App',
  mounted() {

   this.listImoobile()

  },
  methods:{

      async listImoobile(){

          let res     = await axios.get(`/immobles`);
          let data    = res.data

          data.immobles.map((imm)=>{
            let adds = `${imm.street},${imm.number},${imm.city},${imm.state}`
            this.items.push(
              {
                email_contact:imm.email_contact,
                address:adds,
                status:'Hired'
              }
            )
          })

          
      },
      async sotoreImmobile (){

        this.validateFields()

        if(!this.error) {
            try{

              let formData = new FormData();
              formData.append('street', this.street);
              formData.append('number', this.number);
              formData.append('complement', this.complement);
              formData.append('neighborhood', this.neighborhood);
              formData.append('city', this.city);
              formData.append('state', this.state);
              formData.append('email_contact', this.email_contact);

              let res     = await axios.post(`/store`, formData);
              let data    = res.data
            
              if(data.status == 'success'){

                let adds = `${this.street},${this.number},${this.city},${this.state}`
                this.items.push(
                  {
                    email_contact:this.email_contact,
                    address:adds,
                    status:'Hired'
                  }
                )

                this.street = ''
                this.number =='' 
                this.complement = ''
                this.neighborhood = ''
                this.city = ''
                this.state = '' 
                this.email_contact = ''

                this.$swal({
                  icon: 'success',
                  title: 'Success',
                  text: `${data.message}`,
                  timer: 3000
                })

                this.hideModal()

              }
          
          }catch ( error ) {
            this.messageValidate()
          }
        }

      },
      messageValidate (){

          this.$swal({
            icon: 'error',
            title: 'Oops...',
            text: 'Fill in all required fields.',
            timer: 3000
          })
      },
      validateFields(){

        if(
          this.street == '' ||
          this.number == '' ||
          this.neighborhood == '' ||
          this.city == '' ||
          this.state == '' ||
          this.email_contact == ''
        ){
          this.error = true
          this.messageValidate()
        }else if (!this.reg.test(this.email_contact)){
          this.error = true
          this.messageValidate()
        }else if (!this.reg_number.test(this.number)){
          this.error = true
          this.messageValidate()
        }else{
          this.error = false
        }
      },
      showModal() {
        this.$refs['my-modal'].show()
      },
      hideModal() {
        this.$refs['my-modal'].hide()
      },
  }
}
</script>

