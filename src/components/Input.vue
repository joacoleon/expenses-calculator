<template>
    <div class="input-area">
        <input :type="type" :name="name" :value="modelValue" @input="updateValue" required>
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
    emit('update:modelValue', event.target.value);
}

const name = computed(() => {
    return props.label.toLowerCase().replace(" ", "-");
})
</script>

<style scoped>
.input-area {
    display: flex;
    flex-direction: column-reverse;
    gap: 4px;
    width: 100%;
    /* border: 1px solid silver; */
}

input {
    height: 100%;
    width: 100%;
    font-size: 17px;
    border: 2px solid silver;
    border-radius: 3px;
    padding: 4px 5px;
    color: black;
    /* background-color: rgba(255, 255, 255, 0); */
    
}

input:focus {
    caret-color: black;
    border-color: black;
}

.input-label {
    color: silver;
    font-size: 14px;
}

input:focus~.input-label {
    color: black;
}
</style>