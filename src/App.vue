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
                    <i :class="countTsk.checkIn ? classCheckIn : classCheckOut" v-on:click="cheking(index)"></i>
                    <span :class="countTsk.checkIn ? 'checkedContentList' : ''">{{ countTsk.text }}</span>
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
            checkIn: false,
            classCheckIn: 'wasCheck bi bi-check-circle-fill',
            classCheckOut: 'wasCheck bi bi-check-circle'
        }
    },
    methods: {
        addTask() {
            if (this.taskText.trim() === '') return
            this.tasks.push({
               text: this.taskText.trim(),
               checkIn: this.checkIn
            })
            this.taskText = ''
            this.checkIn = false,
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
        cheking(pos) {
            if(!this.tasks[pos].checkIn) document.querySelectorAll('.myListContent span')[pos].setAttribute('class', 'checkedContentList')
            if(this.tasks[pos].checkIn) document.querySelectorAll('.myListContent span')[pos].setAttribute('class', '')
            this.tasks[pos].checkIn = !this.tasks[pos].checkIn
            localStorage.dataList = JSON.stringify(this.tasks)
        }
    },
}
</script>