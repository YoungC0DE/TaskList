<template>
    <div class="container" v-on:keyup.enter="addTask">
        <ul class="background">
            <li v-for="i in 38"></li>
        </ul>
        <h1>TAREFAS</h1>
        <div class="inputArea mb-2">
            <input type="text" class="form-control" id="TaskInput" placeholder="Tarefa" v-model="taskText">
            <i class="bi bi-plus-lg" v-on:click="addTask"></i>
        </div>
        <section id="list" class="myList mb-2">
            <div class="myListContent mb-1" v-for="(countTsk, index) in tasks">
                <div>
                    <i class="wasCheck bi bi-check-circle-fill"></i>
                    <i class="notCheck bi bi-check-circle"></i>
                    {{ countTsk }}
                </div>
                <i class="delete bi bi-x-lg" v-on:click="delTask(index)"></i>
            </div>
        </section>
        <div class="actions">
            <button id="discart" type="button" class="btn" v-on:click="resetList">Descartar</button>
            <button id="save" type="button" class="btn" v-on:click="saveList">Salvar</button>
        </div>
    </div>
</template>

<script>
export default {
    name: 'App',
    data() {
        if(!localStorage.dataList) localStorage.dataList = ''
        return {
            tasks: localStorage.dataList != '' ? localStorage.dataList.split(',') : [],
            taskText: ''
        }
    },
    methods: {
        addTask() {
            if (this.taskText.trim() === '') return
            this.tasks.push(this.taskText)
            this.taskText = ''
            document.getElementById('TaskInput').value = ''
        },
        delTask(pos) {
            this.tasks.splice(pos, 1)
        },
        resetList() {
            this.tasks = []
            this.taskText = ''
            document.getElementById('TaskInput').value = ''
            localStorage.dataList = ''
        },
        saveList() {
            alert('dados salvos!')
            localStorage.dataList = this.tasks
        }
    },
}
</script>