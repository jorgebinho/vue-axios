
import TarefaLista from './components/TarefaLista.vue';

<template>
  <div id="app">
    <div class="jumbotron jumbotron-fluid">
      <div class="container">
        <h1 class="display-4">Requisição com Axios no Vue</h1>
        <p class="lead">Usando a biblioteca axios para fazer chamadas Ajax à uma API REST.</p>
      </div>
    </div>

    <div class="container">
      <TarefaLista />
    </div>
  </div>
</template>

<script>
import TarefaLista from './components/TarefaLista.vue';

export default {
  components: {
    TarefaLista
  }
}
</script>
