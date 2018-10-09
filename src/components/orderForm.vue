<template>
	
	<form action="send" 
            class="order-form">

        <button class="order-form__btn-cancel" 
                v-on:click.prevent="cancelOrder"><span>×</span>
        </button>

        <input type="text"
               id="price"   
               class="order-form-input" 
               placeholder="цена" 
               v-model.number="price">

        <input type="text" 
               class="order-form-input" 
               placeholder="количество" 
               v-model.number="count">

        <div class="order-form-sum">сумма сделки: {{summary}}</div>
        <div class="order-form-com">коммиси: {{commission}}</div>

        <div class="order-form__btns">
          
          <button class="order-form__btn order-form__btn-bay" 
                  v-on:click.prevent="addBayOrder">Купить
          </button>
          
          <button class="order-form__btn order-form__btn-sould" 
                  v-on:click.prevent="addSouldOrder">Продать
          </button>

         

        </div>

      </form>
</template>

<script>
	
	export default {
	props:['usdBalance', 'btcBalance', 'lastPrice'],	
  	data() {
    	
    	return {

    		localUsdBalance: this.usdBalance, 
    		localBtcBalance: this.btcBalance, 
    		localLastPrice: this.lastPrice,

    		price: undefined,
		    count: undefined,
		    minSum: 200,
		    num: 0,
		      
		    orders: [],	
    		labelShow: false,
    		message: ''
	    }
	},
	

	methods:{

	isPriceInTradeInterval(interval){

		let limit = this.lastPriceInterval(interval);

      	if (this.price > limit.upVal || this.price < limit.downVal){

        alert(`цена не должна отличаться от last_price больше чем на 10%, 
               т.е. должна быть в пределах от ${limit.downVal} до ${limit.upVal}.`);
        
		return 0;
      }

      return 1;  
	},	

    isLessMinSum(){

      if(this.curBalance(0) < this.minSum){

        alert(`Минимальное количестов лотов состовляет ${Math.floor(this.minSum/this.price)}`);
        return 0;
      }

      return 1;
    },

    clearOrderForm(){

      this.price = undefined;
      this.count = undefined;
      this.message = '';
    },

    checkBalance(chkdBalance, errorText){

      if(this.curBalance(2) > chkdBalance){

        alert(errorText);
        return 0;
      }
      
      return 1;
    },

    addElementClass(elements, className){

      for (let element in elements) {
        if (elements.hasOwnProperty(element)){
            elements[element].classList.add(className);  
        }
      }
    },

    deleteElemetClass(elements, className){

      for (let element in elements) {
        if (elements.hasOwnProperty(element)){
          if (elements[element].classList.contains(className)){
            elements[element].classList.remove(className);  
          }
        }
      }
    },

    lastPriceInterval(interval){

      let intervalVal = this.localLastPrice*interval/100;
      
      return{    

        upVal: this.localLastPrice + intervalVal,
        downVal: this.localLastPrice - intervalVal
      }

    },

    curBalance(commission){

      return this.price*this.count + this.price*this.count*commission/100;
    },

    addOrder(transactionType){

      this.num += 1; 

      this.orders.push({

        price: this.price,
        count: this.count,
        id: this.num, 
        checked: false,
        tType: transactionType
        
      });
      
    },

    addBayOrder(){
      
      if(!this.isLessMinSum()) return;
      if(!this.checkBalance(this.localUsdBalance, 'У вас недостаточно средств на покупку.')) return;
      if(!this.isPriceInTradeInterval(10)) return;

      this.addOrder(true);
      this.localUsdBalance = this.localUsdBalance - this.curBalance(0);
      this.clearOrderForm();

      this.$emit('addOrder', {orders: this.orders,
      						  usdBalance: this.localUsdBalance,
      						  btcBalance: this.localBtcBalance});

    },

    addSouldOrder(){

      if(!this.isLessMinSum()) return;
      if(!this.checkBalance(this.localBtcBalance, 'У вас недостаточно средств на продажу.')) return;
      if(!this.isPriceInTradeInterval(10)) return;

      this.addOrder(false);
      this.localBtcBalance = this.localBtcBalance - this.curBalance(0);
      this.clearOrderForm();

      this.$emit('addOrder', {orders: this.orders,
      						  usdBalance: this.localUsdBalance,
      						  btcBalance: this.localBtcBalance});
    },

    cancelOrder(){

      this.$emit('addOrder', null);	
      this.clearOrderForm();

    }
  },

  computed:{

    summary(){

      return this.price*this.count || 0;
    },

    commission(){

      return this.price*this.count*2/100 || 0;
    },

  }
}
</script>