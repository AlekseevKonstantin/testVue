<template>
  
  <div class="wrapper"> 
    <div class="user-info">
      
      <h5 class="user-info__last-price">last price:{{lastPrice}}</h5>
      <div class="user-info__balance">
        <h5 class="user-info__usd">USD balance: {{usdBalance}}</h5>
        <h5 class="user-info__btc">btcBalance: {{btcBalance}}</h5>
      </div>

    </div>

      <app-order-form v-bind:createOrderState="createOrderState"
                      v-bind:usdBalance="usdBalance"
                      v-bind:btcBalance="btcBalance"
                      v-bind:lastPrice="lastPrice"
                      v-on:addOrder="getFormData"
                      v-show="createOrderState">
                      

      </app-order-form>  

      <div class="order-table-wrapper">
      <table class="order-table">
        <thead>
          <tr>
            <th>номер</th>
            <th>цена</th>
            <th>количество</th>
            <th>сумма</th>
            <th></th>
          </tr>
        </thead>
        
        <tbody>
          <AppOrder v-for="order in orders" 
                    v-bind:key="order.id" 
                    v-bind:order="order">
          </AppOrder>     
        </tbody> 
        
      </table>

      <div class="order-table-btns">
        
        <button class="order-table__btn order-form__btn-bay" 
                v-on:click.prevent="createOrder">Создание
        </button>
        
        <button class="order-table__btn order-form__btn-sould"
                v-on:click.prevent="deleteOrder">Отмена
        </button>
      </div>
    </div>  
  </div>  
</template>

<script>

import Order from './components/order.vue';
import OrderForm from './components/orderForm.vue';

export default {

  name: 'app',
  data () {
    return {

      usdBalance: 1000,
      btcBalance: 1000,

      lastPrice: 20,

      createOrderState: false,

      orders: []
    }
  },

  methods:{

    orderFormShow(){

       this.createOrderState = true; 
    },

    orderFormHide(){

      this.createOrderState = false;
    },

    deleteOrder(){

      for (let i = 0; i < this.orders.length; i++) {
        
        if (this.orders[i].checked){

          if(this.orders[i].tType){ 
          
            this.usdBalance = this.usdBalance+this.orders[i].price*this.orders[i].count;
          }else{

            this.btcBalance = this.btcBalance+this.orders[i].price*this.orders[i].count; 
          }
          this.orders.splice(i , 1);
          i -= 1;
        }
      }
    },

    createOrder(){

      this.orderFormShow();
    },

    getFormData(res){

      if(res !== undefined && res !== null){

        this.usdBalance = res.usdBalance;
        this.btcBalance = res.btcBalance;
        this.orders = res.orders;
      }
      this.orderFormHide();
    }
  },

  components:{

    AppOrder: Order,
    AppOrderForm: OrderForm
  }
}

</script>

<style>

*{

  box-sizing: border-box;
}


html{

  font-family: sans-serif;
}

body{

  color: rgba(51, 51, 51, .8);
}

.wrapper{

  display: flex;
  flex-direction: column;
  align-items: center;

}

form{

  border: 1px solid #e9ecef;
  padding: 4rem 2.25rem 2rem 2.25rem;
}


h5{

  font-size: 1.25rem;
}

button{

  border: none;
  outline: none;
}

.disable{

  pointer-events:  none;
  opacity: .6;
}

  /* user info */
.user-info{

  width: 100%;

  display:  flex;
  align-items: center;
  justify-content: space-around;
}

.user-info__balance{

  display: flex;
  align-items: center;
  justify-content: space-between;
}

.user-info__usd{

  margin-right: 50px;
}

/* order form */
.order-form{
  
  position: relative;

  display:  flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

  margin-top: 100px;

  max-width: 60%;
}

.order-form-input, 
.order-form-sum,
.order-form-com{

  padding: 10px;
  margin-top: 10px;
  font-size: 16px;
}

.order-form-sum,
.order-form-com{
  
  width: 100%;
  text-align: left;

}

.order-form-com{

  margin-top: 0;
} 

.order-form__btns{

  margin-top: 30px;
}


.order-form__btn-cancel{
  
  position: absolute;

  top: 5%;
  right: 5%;

  font-size: 1.5rem;
  border-radius: 50%;
  
  display: flex;
  justify-content: center;
  align-items: center;

  transition: color .4s;

}

.order-form__btn-cancel > span{

  display: inline-block;
}

.order-form__btn, 
.order-table__btn{
  
  font-size: 16px;
  padding: 0.7rem 1rem;
  border-radius: 20px;
  border: none;

  margin-right: 10px;
}

.order-form__btn:last-child{

  margin-right: 0;
}

.order-form__btn:hover,
.order-form__btn-cancel:hover,
.order-table__btn:hover{

  cursor: pointer;
}

.order-form__btn-cancel{

  background-color: #ffdc60;
}

.order-form__btn-bay{

  background-color: #6EC7AB;
}

.order-form__btn-sould{

  background-color: #F57A7C;
}

/* order table */

.order-table-wrapper{

  width: 100%;
  display: flex;

  flex-direction: column;
  
  align-items: center;
  justify-content: center;

  margin-top: 100px;
}

.order-table {

  border-collapse: collapse;
  width: 70%;
}

.order-table thead th{
  
  vertical-align: bottom;
  border-bottom: 2px solid #dee2e6;
  padding: .75rem;

  font-size: 1.25rem !important;
  font-weight: bold;
  line-height: 1.5;
}

.order-table td,
.order-table th{

  background-color: #fff;
  border: 1px solid #dee2e6;
}

.order-table td{

  padding-left: 10px;
  min-width: 5%;
}

.order-table-btns{

  margin-top: 50px;
}

.td-center{

  text-align: center;
}

</style>
