<template>
  <div id="app">
    <modelagem />
  </div>
</template>

<script>
import Empresa from "./components/Empresa.vue";
import Fornecedor from "./components/Fornecedor.vue";
import Cliente from "./components/Cliente.vue";
import Modelagem from "./components/Modelagem.vue";
import sidebar from "./components/SideBar.vue";

export default {
  name: "app",
  components: {
    Modelagem,
    //GraphWrapper
    //Fornecedor,
    //Cliente,
    //Modelagem,
    //sidebar,
    //Empresa,
    //Fornecedor,
  },
};
</script>

<style>
</style>
