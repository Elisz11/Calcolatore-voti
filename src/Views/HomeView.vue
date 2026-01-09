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
    <div class="min-h-screen from-blue-50 to-indigo-100 p-8">
        <div class="max-w-7xl mx-auto">
            <h1 class="text-5xl font-bold text-gray-800 mb-12 text-center">Calcolatore Media Voti</h1>
            
            <div class="grid grid-cols-3 gap-6">
                <!-- Input Section -->
                <div class="bg-white rounded-2xl shadow-xl p-8">
                    <div class="space-y-6">
                        <div>
                            <label class="block text-lg font-semibold text-gray-700 mb-3">Voto</label>
                            <input type="number" class="w-full px-4 py-3 border-2 border-gray-300 rounded-xl focus:border-blue-500 focus:outline-none text-lg" v-model="voto" min="0" max="10" pattern="[0-9]+(\.[0-9]{1,2})?" @keyup.enter="inserisci">
                            <button class="w-full mt-3 rounded-xl bg-blue-500 hover:bg-blue-600 border-0 text-white font-semibold py-3 px-6 transition duration-200" @click="inserisci">Inserisci</button>
                        </div>

                        <div class="border-t-2 border-gray-200 pt-6">
                            <label class="block text-lg font-semibold text-gray-700 mb-3">Obiettivo</label>
                            <input type="number" class="w-full px-4 py-3 border-2 border-gray-300 rounded-xl focus:border-indigo-500 focus:outline-none text-lg" min="0" max="10" pattern="[0-9]+(\.[0-9]{1,2})?" v-model="obiettivo" @keyup.enter="calcolaObiettivo">
                            <button class="w-full mt-3 rounded-xl bg-blue-500 hover:bg-blue-600 border-0 text-white font-semibold py-3 px-6 transition duration-200" @click="calcolaObiettivo">Inserisci</button>
                        </div>
                    </div>
                </div>

                <!-- Votes List -->
                <div class="bg-white rounded-2xl shadow-xl p-8">
                    <h2 class="text-2xl font-bold text-gray-800 mb-6">Voti Inseriti</h2>
                    <div class="space-y-2 max-h-96 overflow-y-auto">
                        <div v-if="voti.length === 0" class="text-center text-gray-400 py-12">
                            Nessun voto
                        </div>
                        <div v-for="i in voti" :key="i.id" class="flex justify-between items-center bg-gray-50 p-4 rounded-xl hover:bg-gray-100 transition border border-gray-200">
                            <span class="text-gray-600 font-medium">{{ i.id }}.</span>
                            <span class="text-4xl font-bold text-blue-600">{{ i.value }}</span>
                            <button class="rounded-lg bg-red-500 hover:bg-red-600 text-white px-4 py-2 border-0 font-medium transition" @click="rimuovi(i.id)">Rimuovi</button>
                        </div>
                    </div>
                </div>

                <!-- Stats Section -->
                <div class="bg-white rounded-2xl shadow-xl p-8 space-y-6">
                    <div class="bg-linear-to-br from-blue-50 to-blue-100 rounded-xl p-6">
                        <h1 class="text-gray-700 font-semibold text-sm mb-2">Media</h1>
                        <span class="text-5xl font-bold text-blue-600">{{ media }}</span>
                    </div>

                    <div class="bg-linear-to-br from-indigo-50 to-indigo-100 rounded-xl p-6">
                        <h1 class="text-gray-700 font-semibold text-sm mb-2">Voto necessario per obiettivo</h1>
                        <span class="text-5xl font-bold text-indigo-600">{{ votoNecessario }}</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>