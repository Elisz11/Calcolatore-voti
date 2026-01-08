<script setup>
    import { ref, watch } from 'vue';
    
    const voto = ref();
    const voti = ref([]);
    const media = ref(0);
    const obiettivo = ref();
    const votoNecessario = ref(0);

    function inserisci() {
        if (voto.value == null || !/^[0-9]+(\.[0-9]{1,2})?$/g.test(voto.value) || voto.value < 0 || voto.value > 10)
            return;

        if (voti.value.length == 0)
            voti.value.push({
                id: 0,
                value: voto.value
            })
        else {
            voti.value.push({
                id: voti.value[voti.value.length - 1].id + 1,
                value: voto.value
            })
        }
        voto.value = null;
    }

    function rimuovi(id) {
        for (let i = 0; i < voti.value.length; i++) {
            if (voti.value[i].id == id) {
                voti.value.splice(i, 1);
                break;
            }
        }
    }

    function calcolaMedia() {
        let somma = 0;

        if (voti.value.length == 0)
            return 0;

        for (let i = 0; i < voti.value.length; i++) {
            somma += voti.value[i].value;
        }
        media.value = (somma / voti.value.length).toFixed(2);
    }

    function calcolaObiettivo() {
        votoNecessario.value = ((obiettivo.value * (voti.value.length + 1)) - (media.value * voti.value.length)).toFixed(2);
    }

    watch(voti, () => {
        calcolaMedia();
        calcolaObiettivo();
    }, { deep: true });

</script>

<template>
    <div class="flex columns-3 justify-between *:flex *:flex-col *:w-full">
        <div class="p-4 gap-1">
            <label>Voto</label>
            <input type="number" class=" border rounded-lg" v-model="voto" min="0" max="10" pattern="[0-9]+(\.[0-9]{1,2})?" @keyup.enter="inserisci">
            <button class="rounded-lg bg-blue-400 border" @click="inserisci">Inserisci</button>

            <label>Obiettivo</label>
            <input type="number" class=" border rounded-lg" min="0" max="10" pattern="[0-9]+(\.[0-9]{1,2})?" v-model="obiettivo">
            <button class="rounded-lg bg-blue-400 border" @click="calcolaObiettivo">Inserisci</button>
        </div>
        <div>
            <div v-for="i in voti" class="w-full flex justify-between p-4 gap-2 border-b">
                <span>{{ i.id }}.</span>
                <span class=" text-5xl">{{ i.value }}</span>
                <button class="border rounded-lg bg-red-500 p-2" @click="rimuovi(i.id)">Rimuovi</button>
            </div>
        </div>
        <div>
            <h1>Media:</h1>
            <span>{{media}}</span>

            <h1>Voto necessario per obiettivo</h1>
            <span>{{ votoNecessario }}</span>
        </div>
    </div>
</template>