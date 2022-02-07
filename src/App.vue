<template>
  <div class="body">
    <div class="head">
      <div id="app">
        <product-registration :products.sync="products" />
        <calculate-price-each-person-from-products :products="products" />
      </div>
    </div>
  </div>

</template>

<script>

import ProductRegistration from "@/components/ProductRegistration";
import CalculatePriceEachPersonFromProducts from "@/components/CalculatePriceEachPersonFromProducts";
export default {
  name: 'App',
  components: {CalculatePriceEachPersonFromProducts, ProductRegistration},
  data() {
    return {
      products: []
    }
  },
  methods: {},
  computed: {}
}
</script>

<style scoped>
 .body{
   height: 800px;
   width: 100%;
   display: flex;
   justify-content: center;
   background-image: linear-gradient(dodgerblue, #ccc);
 }
 .head{
   width: 80%;
 }
</style>