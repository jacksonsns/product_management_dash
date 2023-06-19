<template>
  <div class="container mt-4 mb-5" v-if="!showForm">
    <div class="row">
      <div class="col-12">
        <div class="d-flex justify-content-end mb-3">
          <router-link class="btn btn-success" to="/cadastrar-produto">Cadastrar Produto</router-link>
        </div>
        <h4>Lista de Produtos</h4>
        <div class="input-group mb-3">
          <input type="text" class="form-control" v-model="searchEmployees" @input="search" placeholder="Buscar produto"/>
        </div>
        <table class="table table-striped table-hover table-bordered table-responsive">
          <thead>
            <tr>
              <th>Nome</th>
              <th>Descrição</th>
              <th>Tensão</th>
              <th>Referência</th>
              <th>Marca</th>
              <th>Categoria</th>
              <th>Ação</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="product, index in products" :key="index">
              <td>{{ product.name }}</td>
              <td>{{ product.description }}</td>
              <td>{{ product.voltage }}</td>
              <td>{{ product.reference }}</td>
              <td>{{ product.brand }}</td>
              <td>{{ product.category.name }}</td>
              <td>
                <a class="btn btn-primary ml-5" href="#" role="button" @click="openForm(product)">
                  <i class="fas fa-pencil-alt"></i>
                </a>
                <a class="btn btn-danger ml-5 btn-trash" href="#" role="button" @click="deleteProduct(product.id)">
                  <i class="fas fa-trash-alt"></i>
                </a>
              </td>
            </tr>
            <span v-if="msg_not_found" class="text-center">{{ msg_not_found }}</span>
          </tbody>
        </table>
      </div>
    </div>
  </div>
  <form-edit-product v-if="showForm" :product="selectedProduct"></form-edit-product>
  
</template>

<script>
import axios from 'axios';
import FormEditProduct from '@/components/FormEditProduct.vue'

export default {
  components: {
    FormEditProduct
  },
  name: "HomeView",
  props: {
    showForm: Boolean
  },

  data() {
    return {
      showForm: false,
      products: [],
      searchProducts: '',
      msg_not_found: '',
      form: {
        name: '',
        value_price: '',
        description: '',
        reference: '',
        brand: '',
        voltage: '',
        category_id: '',
        selectedProduct: null,
      },
    }
  },

  created() {
    const accessToken = this.$store.state.token;

    axios.get('http://localhost:8000/api/products', {
      headers: {
        'Authorization': 'Bearer ' + accessToken,
      },
    })
    .then(response => {
      const responseData = response.data.data;
      this.products = responseData;
    })
    .catch(error => {
      console.log(error);
    });
  },

  methods: {
    openForm(product) {
      this.form = {
        id: product.id,
        name: product.name,
        value_price: product.value_price,
        description: product.description,
        reference: product.reference,
        brand: product.brand,
        voltage: product.voltage,
        category_id: product.category.id
      };
      this.showForm = true;
      this.selectedProduct = product;
    },

    deleteProduct(product_id) {
      const accessToken = this.$store.state.token;
      
      if (confirm('Tem certeza que deseja excluir o produto?')) {
        axios.delete(`http://localhost:8000/api/products/${product_id}`, {
          headers: {
            'Authorization': 'Bearer ' + accessToken,
          },
        })
        .then(response => {
          if (response.status >= 200 && response.status < 300) {
            alert('Produto removido com sucesso!');
            
            // Remover o produto da lista localmente
            const index = this.products.findIndex(product => product.id === product_id);
            if (index !== -1) {
              this.products.splice(index, 1);
            }
          }
        })
        .catch(error => {
          console.log(error);
        });
      }
    },

    search() {
      axios.get('http://localhost:8000/api/funcionarios/search?search='+ this.archProducts)
      .then(response => {
        if(this.searchEmployees != '') {
          if(response.data.status == 2) {
            this.msg_not_found = response.data.msg
            this.products = []
            return
          }
          this.colaboradores = response.data.response
          this.msg_not_found = ''
        } else {
          axios.get('http://localhost:8000/api/administrador')
          .then(response => {
            this.products = response.data.data
            this.msg_not_found = ''
          })
          .catch(error => {
            console.log(error)
          })
        }
        
      })
      .catch(error => {
        console.log(error)
      })
    },

  }
};
</script>

<style>
  .btn-trash {
    margin-left: 10px;
  }

</style>
