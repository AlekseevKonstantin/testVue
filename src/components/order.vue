<template>
  
    <tr v-bind:style="[bay, sould]">
      <th>{{order.id}}</th>
      <td>{{order.price}}</td>
      <td>{{order.count}}</td>
      <td>{{summary}}</td>
      <th><input type="checkbox" v-model="order.checked"/></th>
    </tr>

</template>

<script>
  export default {

    props:['order'],

    data () {
        return {
        
      }
    },

    computed: {

      summary(){

        let sum = this.order.price * this.order.count;
        return sum;
      },

      bay(){

        let res = this.order.tType && {'color': '#6EC7AB !important'};
        return res;
      },

      sould(){

        let res = !this.order.tType && {'color': '#F57A7C !important'};
        return res;
      }

    }
}

</script>


