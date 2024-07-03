<template>
    <div class="input-area">
        <input :type="type" :name="name" :value="modelValue" @input="updateValue" required>
        <div class="underline"></div>
        <div class="input-label">{{ label }}</div>
    </div>
</template>

<script setup>
import {computed} from 'vue';
    const props = defineProps({
    type: {
        type: String,
        required: true,
    },
    label: {
        type: String,
        required: true,
    },
    modelValue: {
        type: String,
        required: false
    }
})

const emit = defineEmits(['update:modelValue'])

const updateValue = (event) => {
    emit('update:modelValue', event.target.value)
}

const name = computed(() => {
    return props.label.toLowerCase().replace(" ", "-");
})
</script>

<style scoped>
.input-area {
    height: 30px;
    width: 100%;
    position: relative;
}

input {
    height: 100%;
    width: 100%;
    font-size: 17px;
    border: none;
    border-bottom: 2px solid silver;
    color: black;
}

input:focus {
    caret-color: black;
}

.input-label {
    position: absolute;
    bottom: 10px;
    left: 0;
    color: silver;
    pointer-events: none;
    transition: all 0.3s ease;
}

input:focus~.input-label,
input:valid~.input-label {
    transform: translateY(-20px);
    font-size: 15px;
}

input:focus~.input-label {
    color: black;
}

.underline {
    position: absolute;
    bottom: 0px;
    height: 2px;
    width: 100%;
}

.underline::before {
    position: absolute;
    content: "";
    height: 100%;
    width: 100%;
    background: black;
    transform: scaleX(0);
    transition: transform 0.3s ease;
}

input:focus~.underline::before,
input:focus~.underline::before {
    transform: scaleX(1);
}

/* Chrome, Safari, Edge, Opera */
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

/* Firefox */
input[type=number] {
    -moz-appearance: textfield;
    appearance: textfield;
}
</style>