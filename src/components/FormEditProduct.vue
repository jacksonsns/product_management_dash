<template>
  <div class="container mt-4 mb-5 form-overlay">
    <form @submit.prevent="submitForm">
      <div class="row">
        <h4>Editar Produtos</h4>
        <div class="col-sm-12 col-md-6 mt-2">
          <div class="form-group">
            <label for="name">Nome</label>
            <input id="name" class="form-control" type="text" v-model="form.name" required>
          </div>
        </div>
        <div class="col-sm-12 col-md-6 mt-2">
          <div class="form-group">
            <label for="value_price">Preço</label>
            <input id="value_price" class="form-control" type="number" v-model="form.value_price" required>
          </div>
        </div>
        <div class="col-sm-12 mt-2">
          <div class="form-group">
            <label for="description">Descrição</label>
            <textarea id="description" class="form-control" v-model="form.description" required></textarea>
          </div>
        </div>
        <div class="col-sm-12 col-md-6 mt-2">
          <div class="form-group">
            <label for="reference">Referência</label>
            <input id="reference" class="form-control" type="text" v-model="form.reference" required>
          </div>
        </div>
        <div class="col-sm-12 col-md-6 mt-2 mt-2">
          <div class="form-group">
            <label for="brand">Marca</label>
            <select id="brand" class="form-control" v-model="form.brand" required>
              <option value="" disabled selected>Selecione uma marca</option>
              <option value="Electrolux">Electrolux</option>
              <option value="Brastemp">Brastemp</option>
              <option value="Fischer">Fischer</option>
              <option value="Samsung">Samsung</option>
              <option value="LG">LG</option>
            </select>
          </div>
        </div>
        <div class="col-sm-12 col-md-6 mt-2">
          <div class="form-group">
            <label for="voltage">Tensão</label>
            <select id="voltage" class="form-control" v-model="form.voltage" required>
              <option value="" disabled selected>Selecione uma tensão</option>
              <option value="110v">110v</option>
              <option value="220v">220v</option>
            </select>
          </div>
        </div>
        <div class="col-sm-12 col-md-6 mt-2">
          <div class="form-group">
            <label for="category">Categoria</label>
            <select id="category" class="form-control" v-model="form.category_id" required>
              <option value="" disabled selected>Selecione uma categoria</option>
              <option v-for="category in categories" :key="category.id" :value="category.id">{{ category.name }}</option>
            </select>
          </div>
        </div>
        <div class="col-sm-12 mt-3">
          <button class="btn btn-primary" type="submit">Salvar</button>
          <button class="btn btn-danger" type="button" @click="goToHome">Cancelar</button>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  props: {
    product: Object,
  },
  data() {
    return {
      form: {
        name: '',
        value_price: '',
        description: '',
        reference: '',
        brand: '',
        voltage: '',
        category_id: ''
      },
      categories: [],
    };
  },
  watch: {
    product: {
      immediate: true,
      handler(newProduct) {
        if (newProduct) {
          this.form.id = newProduct.id;
          this.form.name = newProduct.name;
          this.form.value_price = newProduct.value_price;
          this.form.description = newProduct.description;
          this.form.reference = newProduct.reference;
          this.form.brand = newProduct.brand;
          this.form.voltage = newProduct.voltage;
          this.form.category_id = newProduct.category_id;
        }
      }
    }
  },
  created() {
    this.loadCategories();
  },
  methods: {
    loadCategories() {
      const accessToken = this.$store.state.token;
      axios.get('http://127.0.0.1:8000/api/categories', {
        headers: {
          'Authorization': 'Bearer ' + accessToken,
        },
      })
        .then(response => {
          this.categories = response.data.data;
        })
        .catch(error => {
          console.log(error);
        });
    },
    submitForm() {
      const accessToken = this.$store.state.token;
      axios.put(`http://127.0.0.1:8000/api/products/${this.form.id}`, this.form, {
        headers: {
          'Authorization': 'Bearer ' + accessToken,
        }
      })
      .then(response => {
        if (response.status >= 200 && response.status < 300) {
          alert('Produto editado com sucesso!');
          window.location.reload();
        }
      })
      .catch(error => {
        if (error.response.data.errors) {
          const errorMessage = Object.values(error.response.data.errors).join('\n');
          alert(errorMessage);
        }
      });
    },
    goToHome() {
      window.location.reload();
    }
  }
}
</script>