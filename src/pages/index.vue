<script setup>
    import { ref, reactive} from 'vue'

    let tasques = reactive(JSON.parse(localStorage.getItem("tasks")) || [])

    const order = ref(true);
    const dialog = ref(false);
    const confirmation = ref(false);
    const edit = ref(0);
    const title = ref(null);
    const desc = ref(null);
    const completed = ref(false);

    tasques.sort((a, b) => b.id - a.id)
    console.log(tasques)

    function changeCompleted(index){
        tasques[index].completed = !tasques[index].completed;
        console.log(tasques)

        localStorage.setItem("tasks", JSON.stringify(tasques));
    }

    function deleteMessage(index){
        edit.value = index;
        confirmation.value = true;
    }

    function deleteTask(){
        tasques.splice(edit.value, 1);

        localStorage.setItem("tasks", JSON.stringify(tasques));

        confirmation.value = false;
    }

    function changeOrder(){
        order.value = !order.value;
        tasques.sort((a, b) => (!order.value) ? a.id - b.id : b.id - a.id);
        console.log(tasques)
    }

    function openDialog(index){
        edit.value = index;

        title.value = tasques[edit.value].title;
        desc.value = tasques[edit.value].desc;
        completed.value = tasques[edit.value].completed;

        dialog.value = true;
    }

    function saveNote(){
        tasques[edit.value].title = title.value;
        tasques[edit.value].desc = desc.value;
        tasques[edit.value].completed = completed.value;

        localStorage.setItem("tasks", JSON.stringify(tasques));
        dialog.value = false;
    }
</script>

<template>
    <v-container>
        <v-row>
            <v-col cols="12">
                <v-div class="d-flex justify-end">
                    <v-btn icon variant="plain" @click="changeOrder">
                        <v-icon class="text-white">{{ (order) ? "mdi-sort-ascending" : "mdi-sort-descending" }}</v-icon>
                    </v-btn>
                </v-div>
            </v-col>
            <v-col cols="12" lg="4" sm="6" v-for="(task, index) in tasques" :key="task.id">
                <v-sheet v-if="!task.completed" class="pa-5 border border-thick position-relative">
                    <p class="position-absolute right-0 top-0 pt-3 pr-4 ma-0 text-grey-darken-1 text-body-medium">{{ task.date }}</p>
                    <p class="position-absolute right-0 top-0 pt-8 pr-4 ma-0 text-grey-darken-1 text-body-small">{{ task.time }}</p>
                    <v-btn class="pa-0 ma-0 mb-2 mt-2" variant="plain" @click="changeCompleted(index)"><h2>{{ task.title }}</h2></v-btn>
                    <p class="text-grey-lighten-1 ma-0 pb-1">{{ task.desc }}</p>
                    <v-div class="d-flex justify-end">
                        <v-btn icon variant="plain" @click="openDialog(index)">
                            <v-icon class="text-white mt-1">mdi-border-color</v-icon>
                        </v-btn>
                        <v-btn icon variant="plain" @click="deleteMessage(index)">
                            <v-icon class="text-white">mdi-trash-can-outline</v-icon>
                        </v-btn>
                    </v-div>
                </v-sheet>
                <v-sheet v-else class="pa-5 border border-thick position-relative">
                    <p class="position-absolute right-0 top-0 pt-3 pr-4 ma-0 text-grey-darken-1 text-body-medium">{{ task.date }}</p>
                    <p class="position-absolute right-0 top-0 pt-8 pr-4 ma-0 text-grey-darken-1 text-body-small">{{ task.time }}</p>
                    <v-btn class="pa-0 ma-0 mb-2 mt-2" variant="plain" @click="changeCompleted(index)"><h2 class="text-decoration-line-through text-grey">{{ task.title }}</h2></v-btn>
                    <p class="text-decoration-line-through text-grey-darken-1 ma-0 pb-1">{{ task.desc }}</p>
                    <v-div class="d-flex justify-end">
                        <v-btn icon variant="plain" @click="openDialog(index)">
                            <v-icon class="text-white mt-1">mdi-border-color</v-icon>
                        </v-btn>
                        <v-btn icon variant="plain" @click="deleteMessage(index)">
                            <v-icon class="text-white">mdi-trash-can-outline</v-icon>
                        </v-btn>
                    </v-div>
                </v-sheet>
            </v-col>
            <v-col v-if="tasques.length == 0" cols="12">
                <h2 class="text-center text-grey">No hi ha cap tasca</h2>
                <v-div class="d-flex justify-center">
                    <v-btn color="orange-darken-1" size="large" to="/newNote">Crear tasca</v-btn>
                </v-div>
                
            </v-col>
        </v-row>
    </v-container>
    <v-dialog v-model="dialog">
        <div class="d-flex justify-center" >
            <v-card class="pa-5 w-100 border border-thick" max-width="800">
                <h2>Editar tasca</h2>
                <v-text-field label="Titol" placeholder="Nom de la tasca" v-model="title"></v-text-field>
                <p v-if="titleRequired" class="ma-0 pb-5 text-body-small text-red">*Omplir aquest camp es obligatori</p>
                <v-textarea label="Descripcio" placeholder="Describeix la tasca"  v-model="desc"></v-textarea>
                <p v-if="descRequired" class="ma-0 pb-5 text-body-small text-red">*Omplir aquest camp es obligatori</p>
                <v-switch label="Completada" inset color="amber-accent-3" base-color="grey-darken-1" thumb-color="white" v-model="completed"></v-switch>
                <v-div class="d-flex justify-end ga-3">
                    <v-btn color="red-darken-1" size="large" justify="end" @click="dialog = false">Cancelar</v-btn>
                    <v-btn color="orange-darken-1" size="large" justify="end" @click="saveNote">Guardar</v-btn>
                </v-div>
            </v-card>
        </div>
    </v-dialog>
    <v-dialog v-model="confirmation">
        <div class="d-flex justify-center" >
            <v-card class="pa-5 w-100 border border-thick" max-width="500">
                <p class="pb-3">Segur que vols eliminar la tasca?</p>
                <v-div class="d-flex justify-end ga-3">
                    <v-btn color="red-darken-1" size="large" justify="end" @click="confirmation = false">Cancelar</v-btn>
                    <v-btn color="orange-darken-1" size="large" justify="end" @click="deleteTask">Eliminar</v-btn>
                </v-div>
            </v-card>
        </div>
    </v-dialog>
</template>
