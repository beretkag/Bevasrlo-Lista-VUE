<template>
  <div class="about d-flex align-items-center flex-column">
    <div>
      <img alt="Lista logo" src="../assets/lista.png">
    </div>
    <div class="w-50">
      <h3>Lista elem felvétele</h3>
      <div class="input-group mb-3">
        <span class="input-group-text">Termék:</span>
        <select class="form-select form-select-sm" v-model="newItem.product">
          <option v-for="product, index in products" :key="product._id" :value="product" :selected="index < 1">{{ product.name }} ({{ product.price }} Ft / {{ product.unit }})</option>
        </select>
        <span class="input-group-text">Mennyiség: </span>
        <input type="number" class="form-control" v-model="newItem.quantity">
        <span class="input-group-text" style="min-width: 80px">
          {{ selectedUnit() }}
        </span>
      </div>
      <button class="btn btn-success" @click="addItem()">
        Árucikk felvétele<i class="bi bi-upload m-2"></i>
      </button>
    </div>
  </div>
</template>

<script>
//import axios from "axios";

import axios from 'axios';

export default {
  name: 'HomeView',
  components: {},
  data() {
    return{
      newItem: {},
      items: [],
      products: []
    };
  },

  computed: {
    ProductsSummary() {
      return this.products.length;  
    },
    ItemsSummary() {
      return this.items.length;
    }
  },
  
  created() {
    this.newItem.quantity = 1;
    axios.get("http://localhost:5000/List")
    .then(
      (results) => {
        this.items = results.data;
        axios.get("http://localhost:5000/Products")
        .then(
          (res) => {
            this.products = res.data;
            this.items.forEach(item => {
              this.products.forEach(product => {
                if (item.productID == product._id) {
                  item.edit = false;
                  item.newQuantity = item.quantity;
                  item.name = product.name;
                  item.price = product.price;
                  item.unit = product.unit;
                }
              })
            })
          }
        ).catch((err) => console.log(err));
      }
    ).catch((err) => console.log(err));
  },

  methods: {
    addItem(){
      console.log(this.newItem);
    },

    selectedUnit(){
      if (this.newItem.product == null) {
        return "";
      } else {
        return this.newItem.product.unit
      }
    }
  }
}
</script>
