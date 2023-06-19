<template>
  <div class="container">
    <div class="row">
      <div class="col-12 col-md-6 offset-md-3">
        <div class="card bg-light d-flex align-items-center justify-content-center">
          <div class="card-body">
            <h5 class="card-title">Login</h5>
            <form @submit.prevent="submit">
              <div class="form-group">
                <label for="email">Email</label>
                <input id="email" type="email" class="form-control" v-model="form.email" required>
              </div>
              <div class="form-group">
                <label for="password">Senha</label>
                <input id="password" type="password" class="form-control" v-model="form.password" required>
              </div>
              <div class="text-danger text-center" v-if="error">
                {{ error }}
              </div>
              <div class="form-group mt-3">
                <button class="btn btn-primary btn-block w-100" type="submit" v-if="!loading">Entrar</button>
                <div class="progress" v-show="loading">
                  <div class="progress-bar progress-bar-striped progress-bar-animated"></div>
                </div>
              </div>
              <div class="text-center">
                <router-link class="btn btn-link" to="/registrar">Registrar</router-link>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import { mapMutations } from 'vuex';
import { Spinner } from 'vue-spinner/dist/vue-spinner.min.js';

export default {
    components: {
      Spinner
    },
    data() {
        return {
          form: {
              email: "",
              password: "",
          },
          error: null,
          loading: false,
        };
    },

    methods: {
        submit() {
          axios
              .post("http://localhost:8000/api/login", this.form)
              .then((response) => {
                  this.loading = true;
                  if (response.status === 200) {
                      this.$store.commit("login", response.data.token);
                      this.$router.push("/");
                  }
                  this.loading = false;
              })
              .catch((error) => {
                  this.error = error.response.data.message
                  console.log(error.response.data.message);
              });
        },

        ...mapMutations({
            login: 'login',
        }),
    },
};
</script>

<style>
 .container {
  margin-top: 10rem;
 }
 .btn-login {
  width: 100%;
  height: 50px;
 }
</style>