<template>
  <div class="home d-flex align-items-center flex-column">
    <div>
      <img alt="Vue logo" src="../assets/logo.png">
    </div>
    <div class="w-50">
      <h3>Termékek felvétele</h3>
      <div class="input-group mb-3">
        <span class="input-group-text" id="basic-addon1">Új termék neve:</span>
        <input type="text" class="form-control" placeholder="Termék neve" v-model="newProduct.name">
      </div>
      <div class="input-group mb-3">
        <span class="input-group-text">Egységár:</span>
        <input type="number" class="form-control" placeholder="Termék ára" v-model="newProduct.price">
        <span class="input-group-text">Ft / </span>
        <input type="text" class="form-control" placeholder="Egység elnevezése (pl.: Liter)" v-model="newProduct.unit">
      </div>
      <button class="btn btn-success" @click="addProduct()">
        Termék felvétele<i class="bi bi-upload m-2"></i>
      </button>

      <hr>

      <div class="accordion accordion-flush" id="accordionFlushExample">
        <div class="accordion-item">
          <h2 class="accordion-header" id="flush-headingOne">
            <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#flush-collapseOne" aria-expanded="false" aria-controls="flush-collapseOne">
              <h4>Termékek</h4>
            </button>
          </h2>
          <div id="flush-collapseOne" class="accordion-collapse collapse" aria-labelledby="flush-headingOne" data-bs-parent="#accordionFlushExample">
            <table class="table table-striped">
              <thead>
                <tr>
                  <th>Termék neve</th>
                  <th></th>
                  <th class="text-end" style="width: 120px;">Egységár</th>
                  <th></th>
                  <th class="text-start">Egység</th>
                  <th>Törlés</th>
                </tr>
              </thead>
              <tbody>
                <tr 
                v-for="product in products" :key="product._id">
                  <td>{{ product.name }}</td>
                  <td class="text-end">
                    <i class="bi bi-pencil btn" @click="editOn(product)" v-if="!product.edit"></i>
                    <i class="bi bi-x-lg btn" v-else @click="editOff()"></i>
                  </td>
                  <td class="text-end" style="width: 120px;">
                    <span v-if="!product.edit">
                      {{ product.price }} Ft
                    </span>
                    <span v-else>
                      <i class="bi bi-check-lg btn" @click="editPrice(product)"></i>
                      <input type="text" style="width: 60px;" v-model="product.newPrice" class="text-end">
                    </span>
                  </td>
                  <td> / </td>
                  <td class="text-start">{{ product.unit }}</td>
                  <td><i class="bi bi-trash3 btn" @click="removeProduct(product._id)"></i></td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'HomeView',
  components: {},

  data() {
    return{
      newProduct: {},
      products: []
    };
  },
  created() {
    axios.get("http://localhost:5000/Products")
    .then(
      (res) => res.data.forEach(item => {
        item.edit = false;
        item.newPrice = item.price;
        this.products.push(item);
      })
    )
    .catch((err) => console.log(err));


  },

  methods: {
    addProduct() {
      if ( this.newProduct.name == null || this.newProduct.price == null )
      {
        alert("Nem adtál meg minden adatot!");
      } else {
        axios
          .post("http://localhost:5000/Products/", this.newProduct)
          .then((res) => {
            this.newProduct._id = res.data.insertedId;
            this.products.push(this.newProduct);
            this.newProduct = {};
          })
          .catch((err) => console.log(err));
      }
    },

    editPrice(product) {

    },

    editOn(product) {
      this.setEditing(this.products);
      product.edit = true;
    },

    editOff() {
      this.setEditing(this.products);
    },

    setEditing(products) {
      products.forEach(item => {
        item.edit = false;
        item.newPrice = item.price;
      });
    },

    removeProduct(id) {
      axios
          .delete("http://localhost:5000/Products/" + id)
          .then((res) => {
            console.log(res);
            let idx = this.products.findIndex((product) => product._id == id);
            this.products.splice(idx, 1);
          })
          .catch((err) => console.log(err));
    }

  }
}
</script>
