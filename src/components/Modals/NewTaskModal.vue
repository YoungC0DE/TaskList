<template>
    <div class="modal fade" id="NewTaskModal" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="NewTaskModal" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">New Task <i class="bi bi-clipboard-check-fill"></i></h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body text-start">
                    <div class="mb-3">
                        <label for="title" class="form-label">Title *</label>
                        <input type="email" class="form-control" id="title" v-model="title" placeholder="Task title">
                    </div>
                    <label for="title" class="form-label">Date and Time (optional)</label>
                    <div class="input-group mb-3">
                        <input id="dateTask" min="2000-01-01" max="2050-12-31" name="dateTask" type="date" class="form-control" v-model="date">
                        <span for="dateTask" class="input-group-text bg-dark text-white">Date</span>
                        <input id="timeTask" min="00:00" max="23:59" name="timeTask" type="time" class="form-control" v-model="time">
                        <span for="timeTask" class="input-group-text bg-dark text-white">Time</span>
                    </div>
                    <div class="mb-3">
                        <label for="description" class="form-label">Description (optional)</label>
                        <Editor :api-key="tiny_key" v-model="description" :init="{
                                plugins: 'anchor autolink charmap codesample emoticons image link lists media searchreplace table visualblocks wordcount checklist mediaembed casechange export formatpainter pageembed linkchecker a11ychecker tinymcespellchecker permanentpen powerpaste advtable advcode editimage tableofcontents footnotes mergetags autocorrect typography inlinecss',
                                toolbar: 'undo redo | blocks fontfamily fontsize | bold italic underline strikethrough | link image media table mergetags | addcomment showcomments | spellcheckdialog a11ycheck typography | align lineheight | checklist numlist bullist indent outdent | emoticons charmap | removeformat',
                                statusbar: false,
                                placeholder: 'Type here...',
                                skin: 'oxide-dark',
                                content_css: 'dark'
                            }" />
                    </div>
                    <div class="col">
                        <p style="margin-bottom: 0.5rem !important;">Priority *</p>
                        <div class="input-group mb-3">
                            <select id="priority" class="form-select" aria-label="Default select example" v-model="priority">
                                <option value="" selected disabled hidden>Select the Priority</option>
                                <option value="1"> Not important </option>
                                <option value="2"> Important </option>
                                <option value="3"> Urgent </option>
                            </select>
                            <span class="input-group-text bg-danger" id="basic-addon1"><i class="bi bi-brightness-alt-high-fill text-white"></i></span>
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal" :disabled="awaitProccess">Close</button>
                    <button type="button" class="btn btn-secondary" disabled v-if="awaitProccess">
                        Loading...
                    </button>
                    <button type="button" class="btn btn-primary" @click="addNewTask()" v-else>Save changes</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import Editor from '@tinymce/tinymce-vue'
import { collection, query, where, getDocs, addDoc, orderBy, limit } from "firebase/firestore";

export default {
    inject: ['db'],
    components: { Editor },
    data() {
        return {
            title: '',
            date: null,
            time: null,
            description: null,
            priority: '',
            awaitProccess: false,
            tiny_key: import.meta.env.VITE_TINYMCE_API_KEY
        }
    },
    methods: {
        validate() {
            if (this.title == '' || this.priority == '') {
                this.$toast.error('It is necessary to fill in all fields with (*)', {
                    position: "top-right"
                })
                return false
            }
            return true
        },
        async addNewTask() {
            if (this.validate() == false) {
                return
            }

            this.awaitProccess = true;

            const userData = JSON.parse(atob(sessionStorage.getItem(btoa('userdata'))))
            const tableItems = collection(this.db, "items");

            // Recover the last ID used in collection 'users'
            const lastItem = await getDocs(query(tableItems, where('fk_user', "==", userData.id), orderBy("ID", "desc"), limit(1)));

            let lastID = 0
            if (!lastItem.empty) {
                lastID = lastItem.docs[0].data().ID
            }

            // Increment the last id and add a new user
            const newID = lastID + 1

            const newItem = {
                ID: newID,
                created_at: Date(),
                date: this.date || '-',
                time: this.time || '-',
                title: this.title,
                description: this.description || '-',
                fk_user: userData.id,
                priority: this.priority,
            };

            addDoc(tableItems, newItem).then(() => {
                window.location.reload()

                // this.$toast.success('Task registered successfully!', {
                //     position: "top-right"
                // })
            }).catch((err) => {
                this.$toast.error(`Error to create the task: ${err}`, {
                    position: "top-right"
                })
            });
        }
    }
}
</script>