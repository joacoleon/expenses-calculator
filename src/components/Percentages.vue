<template>
    <div class="numbers-container">
        <div>
            <p>Aporte JO</p>
            <p>{{ joPercentage ? joPercentage : 0 }}%</p>
        </div>
        <div>
            <p>Total</p>
            <p>${{ incomeSum ? incomeSum : 0 }}</p>
        </div>
        <div>
            <p>Aporte VIKI</p>
            <p>{{ vikPercentage ? vikPercentage : 0 }}%</p>
        </div>
    </div>
</template>

<script setup>
import { computed } from 'vue';
const props = defineProps({
    incomeJo: {
        type: String,
        required: false,
    },
    incomeVik: {
        type: String,
        required: false,
    }
});

const emit = defineEmits(['sendDataToParent'])

const incomeSum = computed(() => {
	return parseInt(props.incomeJo) + parseInt(props.incomeVik) || null;
});

const joPercentage = computed(() => {
	if (props.incomeVik)
		return Math.round(parseInt(props.incomeJo) / incomeSum.value * 100) || null;
});

const vikPercentage = computed(() => {
	if (props.incomeJo){
        let percentage = Math.round(parseInt(props.incomeVik) / incomeSum.value * 100) || null
        emit('sendDataToParent', percentage);
        return percentage;
    }
		
});
</script>import type { loadManifest } from 'astro/app/node';
