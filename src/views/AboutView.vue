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
      <hr>
      



      <div class="accordion" id="accordionPanelsStayOpenExample">
        <div class="accordion-item">
          <h2 class="accordion-header" id="panelsStayOpen-headingOne">
            <button class="accordion-button" type="button" data-bs-toggle="collapse" data-bs-target="#panelsStayOpen-collapseOne" aria-expanded="true" aria-controls="panelsStayOpen-collapseOne">
              Árucikkek ({{ ItemsSummary }})
            </button>
          </h2>
          <div id="panelsStayOpen-collapseOne" class="accordion-collapse collapse show" aria-labelledby="panelsStayOpen-headingOne">
            <div class="accordion-body">
              <table class="table table-striped">
                <thead>
                  <tr>
                    <th>
                      <i class="bi bi-eye-slash btn" v-if="!invisibleCompleted" @click="changeVisibility()"></i>
                      <i class="bi bi-eye btn" v-else @click="changeVisibility()"></i>
                    </th>
                    <th colspan="4" class="text-center">
                      <span class="btn" @click="deleteCompleted()">
                        <i class="bi bi-check2-all"></i>
                        <i class="bi bi-caret-right"></i>
                        <i class="bi bi-trash"></i>
                      </span>
                    </th>
                    <th>
                      <i class="bi bi-trash3-fill btn" @click="deleteAll()"></i>
                    </th>
                  </tr>
                  <tr class="align-middle">
                    <th class="d-flex justify-content-around">
                      Állapot
                    </th>
                    <th>Név</th>
                    <th class="text-end">Mennyiség</th>
                    <th class="text-start">Egység</th>
                    <th class="text-end">Összeg</th>
                    <th>Törlés</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in items" :key="item._id" :class="{ completed: item.completed }" v-show="!invisibleCompleted || !item.completed" class="align-middle">
                    <td>
                      <input type="checkbox" :checked="item.completed" @click="changeChecking(item)">
                    </td>
                    <td>{{ item.name }}</td>
                    <td class="text-end">{{ item.quantity }}</td>
                    <td class="text-start">{{ item.unit }}</td>
                    <td class="text-end">{{ item.quantity*item.price }} Ft</td>
                    <td><i class="bi bi-trash3 btn" @click="removeItem(item._id)"></i></td>
                  </tr>
                </tbody>
                <tfoot>
                  <tr>
                    <td colspan="2">
                      Kifizetett: {{ CompletedValue() }} Ft
                    </td>
                    <td colspan="2">
                      Fennmaradó: {{ IncompletedValue() }} Ft
                    </td>
                    <td colspan="2">
                      Összesen: {{ SummaryValue() }} Ft
                    </td>
                  </tr>
                </tfoot>
              </table>
              
            </div>
          </div>
        </div>
      </div>







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
      products: [],
      invisibleCompleted: false
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
    CompletedValue(){
      let sum = 0;
      for (let i = 0; i < this.ItemsSummary; i++) {
        if (this.items[i].completed) {
          sum += this.items[i].price*this.items[i].quantity;
        }
      }
      return sum;
    },

    IncompletedValue(){
      let sum = 0;
      for (let i = 0; i < this.ItemsSummary; i++) {
        if (!this.items[i].completed) {
          sum += this.items[i].price*this.items[i].quantity;
        }      }
      return sum;
    },

    SummaryValue(){
      let sum = 0;
      for (let i = 0; i < this.ItemsSummary; i++) {
        sum += this.items[i].price*this.items[i].quantity;
      }
      return sum;
    },
    

    deleteCompleted() {
      axios
          .delete("http://localhost:5000/List/completed/true")
          .then(() => {
            for (let i = 0; i < this.ItemsSummary; i++) {
              if (this.items[i].completed) {
                this.items.splice(i, 1);
              }
            }            
          })
          .catch((err) => console.log(err));
    },

    deleteAll() {
      axios
          .delete("http://localhost:5000/List/")
          .then(() => {
            this.items = [];
          })
          .catch((err) => console.log(err));
    },

    changeChecking(item){
      axios({
        method: 'patch',
        url: "http://localhost:5000/List/" + item._id,
        headers:{
            'content-type': 'application/json'
        },
        data: {
          productID : item.productID,
          quantity : item.quantity,
          completed: !item.completed
      }
      }).then(() => {
        item.completed = !item.completed;
      })
    },

    changeVisibility(){
      this.invisibleCompleted = !this.invisibleCompleted;
    },

    removeItem(id){
      axios
          .delete("http://localhost:5000/List/" + id)
          .then(() => {
            let idx = this.items.findIndex((item) => item._id == id);
            this.items.splice(idx, 1);
          })
          .catch((err) => console.log(err));
    },

    addItem(){
      let data = {
        productID: this.newItem.product._id,
        quantity: this.newItem.quantity,
        completed: false
      }

      if ( data.productID == null || !(data.quantity > 0) || data.quantity == null)
      {
        alert("Nem adtál meg minden adatot!");
      } else {
        axios
          .post("http://localhost:5000/List/", data)
          .then((res) => {
            data._id = res.data.insertedId;
            data.name = this.newItem.product.name,
            data.price = this.newItem.product.price,
            data.unit = this.newItem.product.unit,
            this.items.push(data);
            this.newItem = {};
            this.newItem.quantity = 1;
          })
          .catch((err) => console.log(err));
      }
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

<style>

.completed {
  text-decoration: line-through;
}

</style>
