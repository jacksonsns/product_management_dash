<template>
  <nav class="navbar navbar-expand-lg navbar-primary bg-primary">
    <div class="container-fluid">
      <router-link class="navbar-brand text-light" to="/">Gestão de Produtos</router-link>
      <button
        class="navbar-toggler"
        type="button"
        data-mdb-toggle="collapse"
        data-mdb-target="#navbarNav"
        aria-controls="navbarNav"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <i class="fas fa-bars"></i>
      </button>
      <div class="collapse navbar-collapse justify-content-end" id="navbarNav">
        <ul class="navbar-nav">
          <li class="nav-item">
            <router-link class="nav-link active text-light" to="/">Home</router-link>
          </li>
          <li class="nav-item">
            <router-link class="nav-link active text-light" to="/cadastrar-produto">Cadastrar Produto</router-link>
          </li>
        </ul>
        <ul class="navbar-nav ml-auto">
          <li class="nav-item" v-if="!$store.state.token">
            <router-link class="nav-link text-light" href="#" to="/login">Login</router-link>
          </li>
          <li class="nav-item" v-if="$store.state.token">
            <a class="nav-link text-light" href="#" @click="logout">Sair</a>
          </li>
        </ul>
      </div>
    </div>
  </nav>
  <router-view />
</template>

<style lang="scss">

</style>

<script>

export default{
  methods:{
      logout(){
        this.$store.commit('logout');
      }
    },
    isLoggedIn() {
      return this.$store.state.token !== null
    },
    mounted(){
      M.AutoInit()
      this.$store.commit('initializeStore');
    },
}
</script>