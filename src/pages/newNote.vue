<script setup>
    import { reactive, ref } from 'vue'
    import { useRouter } from 'vue-router';

    const router = useRouter();

    let tasques = JSON.parse(localStorage.getItem("tasks"));
    if (tasques === null){
        tasques = reactive([]);
    }

    const title = ref(null);
    const desc = ref(null);
    const completed = ref(false);

    const titleRequired = ref(false);
    const descRequired = ref(false);
    
    function isFine(){
        let fine = ref(true);
        if (title.value == null || title.value == ""){
            titleRequired.value = true;
            fine.value = false;
        }
        if (desc.value == null || desc.value == ""){
            descRequired.value = true;
            fine.value = false;
        }
        return fine.value;
    }

    function createNote(){
        if (!isFine()) return;

        tasques.push({
            id: Date.now(),
            title: title.value,
            desc: desc.value,
            date: String(new Date().toLocaleDateString()),
            time: String(new Date().toLocaleTimeString()),
            completed: completed.value
        })

        localStorage.setItem("tasks", JSON.stringify(tasques));
        router.push('/'); 
    }

</script>

<template>
    <v-btn icon variant="plain" to="/" class="ma-5">
        <v-icon class="text-white">mdi-chevron-left</v-icon>
    </v-btn>
    <v-container>
        <v-row justify="center">
            <v-col cols="12" md="8" sm="10">
                <v-form class="align-center max-w-50 pa-5 border border-thick">
                    <h2>Crear nova tasca</h2>
                    <v-text-field label="Titol" placeholder="Nom de la tasca" v-model="title"></v-text-field>
                    <p v-if="titleRequired" class="ma-0 pb-5 text-body-small text-red">*Omplir aquest camp es obligatori</p>
                    <v-textarea label="Descripcio" placeholder="Describeix la tasca" v-model="desc"></v-textarea>
                    <p v-if="descRequired" class="ma-0 pb-5 text-body-small text-red">*Omplir aquest camp es obligatori</p>
                    <v-switch label="Completada" inset color="amber-accent-3" base-color="grey-darken-1" thumb-color="white" v-model="completed"></v-switch>
                    <v-div class="d-flex justify-end">
                        <v-btn color="orange-darken-1" size="large" @click="createNote">Crear tasca</v-btn>
                    </v-div>
                </v-form>
            </v-col>
        </v-row>
    </v-container>

</template>