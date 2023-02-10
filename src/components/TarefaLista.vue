<template>
    <div>
        <div class="row">
            <div class="col-sm-10">
                <h1 class="font-weight-light">Lista de tarefas</h1>
            </div>
            <div class="col-sm-2">
                <button 
                    class="btn btn-primary float-right" @click="exibirCriarTarefa">
                    <i class="fa fa-plus mr-2" />
                    <span>Criar</span>
                </button>
            </div>
        </div>

        <ul class="list-group" v-if="tarefas.length > 0">
            <TarefasListaItem 
                v-for="tarefa in tarefasOrdenadas"
                :key="tarefa.if"
                :tarefa="tarefa"
                @editar="selecionaTarefaEditar"
                @deletar="deletarTarefa"
                @concluir="editarTarefa"
            />
        </ul>

        <p v-else>Nenhuma tarefa criada</p>

        <TarefaSalvar 
            v-if="exibirFormulario"
            :tarefa="tarefaSelecionada"
            @criar="criarTarefa"
            @editar="editarTarefa"
        />
    </div>
</template>

<script>
import axios from 'axios';
import config from './../../config/config.js';

import TarefaSalvar from './TarefaSalvar.vue';
import TarefasListaItem from './TarefasListaItem.vue';

export default {
    components: {
        TarefaSalvar,
        TarefasListaItem
    },
    data() {
        return {
            tarefas: [],
            exibirFormulario: false,
            tarefaSelecionada: undefined
        }
    },
    computed: {
        tarefasOrdenadas() {
            return this.tarefas.slice().sort((t1, t2) => {
                if(t1.concluido === t2.concluido) {
                    return t1.titulo < t2.titulo
                        ? -1
                        : t1.titulo > t2.titulo
                            ? 1
                            : 0
                }
                return t1.concluido - t2.concluido
            })
        }
    },
    created() {
        axios.get(`${config.apiURL}/tarefas`)
            .then((response) => {
                this.tarefas = response.data;
            })
    },
    methods: {
        criarTarefa(tarefa) {
            axios.post(`${config.apiURL}/tarefas`, tarefa)
                .then((response) => {
                    this.tarefas.push(response.data);
                    this.resetar();
                })
        },
        selecionaTarefaEditar(tarefa) {
            this.tarefaSelecionada = tarefa;
            this.exibirFormulario = true;
        },
        editarTarefa(tarefa) {
            axios.put(`${config.apiURL}/tarefas/${tarefa.id}`, tarefa)
                .then(response => {
                    const indice = this.tarefas.findIndex(t => t.id === tarefa.id);
                    this.tarefas.splice(indice, 1, response.data);
                    this.resetar();
                })
        },
        resetar() {
            this.tarefaSelecionada = undefined;
            this.exibirFormulario = false;
        },
        deletarTarefa(tarefa) {
            const confirmar = window.confirm(`Deseja realmente deletar a tarefa ${tarefa.titulo} ?`);
            if(confirmar) {
                axios.delete(`${config.apiURL}/tarefas/${tarefa.id}`)
                    // eslint-disable-next-line no-unused-vars
                    .then(_response => {
                        const indice = this.tarefas.findIndex(t => t.id === tarefa.id);
                        this.tarefas.splice(indice, 1);
                    }) 
            }
        },
        exibirCriarTarefa() {
            if(this.tarefaSelecionada) {
                this.tarefaSelecionada = undefined;
                return;
            }
            this.exibirFormulario = !this.exibirFormulario;
        }
    }
}
</script>