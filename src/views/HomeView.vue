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
        <span class="input-group-text">Egységár</span>
        <input type="number" class="form-control" placeholder="Termék ára" v-model="newProduct.price">
        <span class="input-group-text">Ft</span>
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
                  <th>Egységár</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <td></td>
                  <td></td>
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
//import axios from "axios";

export default {
  name: 'HomeView',
  components: {},
  data() {
    return{
      newProduct: {}
    };
  }
}
</script>
