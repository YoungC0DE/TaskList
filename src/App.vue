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
                    <i class="wasCheck bi bi-check-circle-fill d-none" v-on:click="unCheck(index)"></i>
                    <i class="notCheck bi bi-check-circle" v-on:click="check(index)"></i>
                    <span>{{ countTsk.text }}</span>
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
        if (!localStorage.dataList) localStorage.dataList = ''
        return {
            tasks: localStorage.dataList != '' ? JSON.parse(localStorage.dataList) : [],
            taskText: '',
            wasCheck: false,
        }
    },
    methods: {
        initialize(pos){
            console.log(pos)
            //this.tasks[pos].wasChecked = true ? document.querySelectorAll('#list .myListContent span')[pos].setAttribute('class', 'checkedContentList') : ''
        },
        addTask() {
            if (this.taskText.trim() === '') return
            this.tasks.push({
               text: this.taskText.trim(),
               wasChecked: this.wasCheck
            })
            this.taskText = ''
            this.wasCheck = false,
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
            this.tasks
            localStorage.dataList = JSON.stringify(this.tasks)
        },
        unCheck(pos) {
            document.querySelectorAll('#list .myListContent span')[pos].removeAttribute('class')
            document.querySelectorAll('.myListContent .notCheck')[pos].setAttribute('class', 'notCheck bi bi-check-circle')
            document.querySelectorAll('.myListContent .wasCheck')[pos].setAttribute('class', 'wasCheck bi bi-check-circle-fill d-none')
            this.tasks[pos].wasChecked = false
            localStorage.dataList = JSON.stringify(this.tasks)
        },
        check(pos) {
            document.querySelectorAll('.myListContent .wasCheck')[pos].setAttribute('class', 'wasCheck bi bi-check-circle-fill')
            document.querySelectorAll('.myListContent .notCheck')[pos].setAttribute('class', 'notCheck bi bi-check-circle d-none')
            document.querySelectorAll('#list .myListContent span')[pos].setAttribute('class', 'checkedContentList')
            this.tasks[pos].wasChecked = true
            localStorage.dataList = JSON.stringify(this.tasks)
        }
    },
}
</script>