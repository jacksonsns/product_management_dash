<template>
  <div class="container mt-4 mb-5">
    <form>
      <div class="row">
        <h4>Cadastre-se</h4>
        <div class="col-sm-12 col-md-6">
          <div class="form-group">
            <label for="nome">Nome</label>
            <input id="nome" type="text" class="form-control" v-model="form.name" required>
          </div>
        </div>
        <div class="col-sm-12 col-md-6">
          <div class="form-group">
            <label for="email">Email</label>
            <input id="email" type="email" class="form-control" v-model="form.email" required>
          </div>
        </div>
        <div class="col-sm-12 col-md-6">
          <div class="form-group">
            <label for="password">Senha</label>
            <input id="password" type="password" class="form-control" v-model="form.password">
          </div>
        </div>
        <div class="col-sm-12 mt-3">
          <button class="btn btn-primary" type="submit" name="action" @click.prevent="submitForm">Cadastrar</button>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      form: {
        name: '',
        email: '',
        password: '',
      },

    };
  },
  methods: {
    submitForm() {
      axios.post('http://127.0.0.1:8000/api/register', this.form)
        .then(response => {
          if (response.status === 200) {
            alert('Usuário cadastrado com sucesso!');
            this.$router.push("/");
          }
        })
        .catch(error => {
          if (error.response.data.errors) {
              const errorMessage = Object.values(error.response.data.errors).join('\n')
              alert(errorMessage)
            }
        });
    }

  }
}
</script>