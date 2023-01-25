<template>
  <div class="about d-flex align-items-center flex-column">
    <div>
      <img alt="Lista logo" src="../assets/lista.png">
    </div>
    <div class="w-50">
      <h3>Lista elem felvétele</h3>
      <div class="input-group mb-3">
        <span class="input-group-text">Termék:</span>

        <select class="form-select form-select-sm">
          <option selected>Válasszon...</option>
          <option value="1">One</option>
        </select>
        <span class="input-group-text">Mennyiség: </span>
        <input type="text" class="form-control" placeholder="Mennyiség">
        <span class="input-group-text"></span>
      </div>
      <button class="btn btn-success">
        Termék felvétele<i class="bi bi-upload m-2"></i>
      </button>
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
      newItem: {},
      items: []
    };
  },
  /*created() {

  },
  methods: {

  }*/
}
</script>
