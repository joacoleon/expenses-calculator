<template>
	<form class="container" autocomplete="off" novalidate>
		<div class="card">
			<div class="incomes-container">
				<div v-for="(income, index) in incomes" :key="index" class="income">
					<CustomInput :type="income.type" :label="income.label" v-model="income.income" />
				</div>
			</div>
		</div>
		<div class="numbers-container">
			<div class="card">
				<p>Aporte JO</p><span>{{ joPercentage }}%</span>
			</div>
			<div class="card">
				<p>Total</p><span>${{ incomeSum }}</span>
			</div>
			<div class="card">
				<p>Aporte VIKI</p><span>{{ vikPercentage }}%</span>
			</div>
		</div>
		<div class="card">
			<div class="expense-container" v-for="(expense, index) in expenses" :key="index">
				<CustomInput :type="expense.type" :label="expense.label" v-model="expense.amount" />
				<div class="expense-buttons">
					<span class="icon-container" :class="expense.paidBy == 'jo' ? 'selected-paidby' : ''"
						v-on:click="onClickPaidBy('jo', index)">
						<i class="fa-solid fa-j fa-xs"></i>
					</span>
					<span class="icon-container" :class="expense.paidBy == 'viki' ? 'selected-paidby' : ''"
						v-on:click="onClickPaidBy('viki', index)">
						<i class="fa-solid fa-v fa-xs"></i>
					</span>
					<span class="icon-container" v-on:click="onClickDelete(index)">
						<i class="fa-solid fa-xmark"></i>
					</span>
				</div>
			</div>
			<div class="add-expense-button-container">
				<button type="button" v-on:click="onClickNewExpense()"><i class="fa-solid fa-plus"></i> Agregar
					gasto</button>
			</div>
			<div class="expense-container" v-if="showNewExpense">
				<CustomInput type="text" label="Nombre" v-model="newExpenseName" />
				<span class="icon-container" v-on:click="addNewExpense()">
					<i class="fa-solid fa-plus"></i>
				</span>
			</div>
		</div>
	</form>
</template>

<script setup>
import { reactive, ref, computed } from 'vue';
import CustomInput from './Input.vue';

const incomes = reactive([
	{
		type: "text",
		label: "Sueldo JO",
		income: null
	},
	{
		type: "text",
		label: "Sueldo VIKI",
		income: null
	}
]);

const expenses = reactive([
	{
		type: "text",
		label: "Alquiler",
		amount: null,
		paidBy: null
	},
	{
		type: "text",
		label: "Super",
		amount: null,
		paidBy: null
	},
	{
		type: "text",
		label: "Seguro",
		amount: null,
		paidBy: null
	},
	{
		type: "text",
		label: "Luz",
		amount: null,
		paidBy: null
	},
	{
		type: "text",
		label: "Gas",
		amount: null,
		paidBy: null
	},
	{
		type: "text",
		label: "Internet",
		amount: null,
		paidBy: null
	},
	{
		type: "text",
		label: "Impuestos auto",
		amount: null,
		paidBy: null
	},
	{
		type: "text",
		label: "Latita",
		amount: null,																	
		paidBy: null
	}
]);

let showNewExpense = ref(false);
let newExpenseName = ref();

const incomeSum = computed(() => {
	return parseInt(incomes[0].income) + parseInt(incomes[1].income) || null;
});

const joPercentage = computed(() => {
	if (incomes[1].income)
		return Math.round(parseInt(incomes[0].income) / incomeSum.value * 100) || null;
});

const vikPercentage = computed(() => {
	if (incomes[0].income)
		return Math.round(parseInt(incomes[1].income) / incomeSum.value * 100) || null;
});

const onClickCheck = (check, index) => {
	expenses[index].enabled = check;

	if (!check) expenses[index].paidBy = null;
}

const onClickDelete = (index) => {
	expenses.splice(index, 1);
}

const onClickPaidBy = (paidBy, index) => {
	if (paidBy == expenses[index].paidBy) {
		expenses[index].paidBy = null;
	} else {
		expenses[index].paidBy = paidBy;
	}
}

const onClickNewExpense = () => {
	showNewExpense.value = true;
}

const addNewExpense = () => {
	if (newExpenseName.value) {
		let newExpense = {
			type: "text",
			label: newExpenseName.value.charAt(0).toUpperCase() + newExpenseName.value.slice(1),
			amount: null,
			paidBy: null
		}

		expenses.push(newExpense);

		newExpenseName.value = null;
		showNewExpense.value = false;
	}

}
</script>

<style scoped>
.container {
	width: 600px;
	margin: 50px 0px;
}

.card {
	width: 100%;
	background: #fff;
	padding: 20px 30px;
	box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.3);
	border-radius: 5px;
}

.incomes-container,
.numbers-container {
	justify-content: space-between;
	gap: 20px;
}

.incomes-container,
.numbers-container,
.expense-container {
	display: flex;
	padding: 10px 0px;
	margin: 10px 0px;
}

.incomes-container {
	gap: 40px;
}

.expense-container {
	gap: 10px;
}

.income {
	flex-grow: 1;
}

.numbers-container>div,
.add-expense-button-container {
	text-align: center;
}

i {
	color: silver;
}

.expense-buttons {
	display: flex;
}

.icon-container {
	/* don't take the whole line */
	display: inline-block;
	/* transition the background change */
	transition: background-color 0.25s;
	/* height of the icon */
	height: 1.5em;
	/* width of the icon */
	width: 1.5em;
	/* space between the icon and the background edge */
	text-align: center;
	padding-top: 0.18em;
	/* make it rounded */
	border-radius: 50%;
}

.selected-paidby,
.icon-container:hover {
	/* add a background on hover */
	background-color: rgba(0, 0, 0, .10);
	/* pointer cursor on hover */
	cursor: pointer;
}

.selected-paidby>i,
.icon-container:hover>i,
.black {
	color: black;
}

.add-expense-button-container>button {
	background: none;
	border: 2px solid silver;
	border-radius: 3px;
	padding: 7px 10px;
	color: silver;
	transition: color 0.25s, border 0.25s;
}

.add-expense-button-container>button>i {
	transition: color 0.25s;
}

.add-expense-button-container>button:hover {
	cursor: pointer;
	color: black;
	border: 2px solid black;
}

.add-expense-button-container>button:hover>i {
	color: black;
}
</style>