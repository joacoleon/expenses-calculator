<template>
	<form class="container" autocomplete="off" novalidate>
		<div class="incomes-numbers">
			<div class="card">
				<div class="incomes-container">
					<div v-for="(income, index) in incomes" :key="index" class="income">
						<CustomInput :type="income.type" :label="income.label" v-model="income.income"
							v-on:keypress="onlyNumbers($event)" />
					</div>
				</div>
			</div>
			<div class="card">
				<!-- <Percentages :incomeJo="incomes[0]?.income" :incomeVik="incomes[1]?.income"
					@sendDataToParent="testEvent" /> -->
				<div class="incomes-placeholder" v-if="!incomesComplete">
					Ingresa ambos sueldos para calcular los porcentajes
				</div>
				<div class="numbers-container" v-if="incomesComplete">
					<div>
						<p>Aporte JO</p>
						<h2>{{ joPercentage ? joPercentage : 0 }}</h2>
						<span> %</span>
					</div>
					<div>
						<p>Total</p>
						<span>$ </span>
						<h2>{{ incomeSum ? incomeSum : 0 }}</h2>
					</div>
					<div>
						<p>Aporte VIKI</p>
						<h2>{{ vikPercentage ? vikPercentage : 0 }}</h2>
						<span> %</span>
					</div>
				</div>
			</div>
		</div>
		<div class="expenses-results">
			<div class="card">
				<div class="expenses">
					<div class="expense-container" v-for="(expense, index) in expenses" :key="index">
						<CustomInput :type="expense.type" :label="expense.label" v-model="expense.amount"
							v-on:keypress="onlyNumbers($event)" />
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
						<button type="button" v-on:click="onClickNewExpense()"><i class="fa-regular fa-plus"></i> Agregar gasto</button>
						<button type="button" v-on:click="onClickSaveAll()"><i class="fa-regular fa-floppy-disk"></i> Guardar</button>
						<button type="button" v-on:click=" "><i class="fa-regular fa-trash-can"></i> Limpiar</button>
					</div>
					<div class="expense-container" v-if="showNewExpense">
						<CustomInput type="text" label="Nombre" v-model="newExpenseName" />
						<div class="expense-buttons">
							<span class="icon-container" v-on:click="addNewExpense()">
								<i class="fa-solid fa-plus"></i>
							</span>
						</div>
					</div>
				</div>
			</div>
			<div class="card">
				<div class="results-placeholder" v-if="!expensesComplete">
					Ingresa ambos sueldos y al menos una expensa para ver los resultados
				</div>
				<div class="results-container" v-if="expensesComplete">
					<div class="total">
						<p>Total de gastos</p>
						<span>$ </span>
						<h2>{{ expensesTotal }}</h2>
					</div>
					<div>
						<p>Gastos que pago JO</p>
						<span>$ </span>
						<h2>{{ expensesPaidByJo }}</h2>
					</div>
					<div>
						<p>Gastos que pago VIKI</p>
						<span>$ </span>
						<h2>{{ expensesPaidByViki }}</h2>
					</div>
					<div>
						<p>JO tiene que poner</p>
						<span>$ </span>
						<h2>{{ joTotal }}</h2>
					</div>
					<div>
						<p>VIKI tiene que poner</p>
						<span>$ </span>
						<h2>{{ vikiTotal }}</h2>
					</div>
					<div class="result">
						<h3>{{ whoOwesWho }}</h3>
					</div>
				</div>
			</div>
		</div>
	</form>
</template>

<script setup>
import { reactive, ref, computed, onMounted } from 'vue';
import CustomInput from './Input.vue';
//import Percentages from './Percentages.vue';

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

const expenses = reactive([]);

const showNewExpense = ref(false);
const newExpenseName = ref(null);

onMounted(() => {
	let savedExpenses = JSON.parse(localStorage.getItem("savedExpenses"));
	incomes[0].income = localStorage.getItem("sueldoJo");
	incomes[1].income = localStorage.getItem("sueldoViki");

	if (savedExpenses) {
		savedExpenses.forEach((e, i) => {
			expenses.push(e);
		})
	} else {
		expenses.push(
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
			})
	}
})

const incomeSum = computed(() => {
	return parseInt(incomes[0].income) + parseInt(incomes[1].income) || null;
});

const joPercentage = computed(() => {
	if (incomes[1].income)
		return (parseInt(incomes[0].income) / incomeSum.value * 100).toFixed(2) || null;
});

const vikPercentage = computed(() => {
	if (incomes[0].income)
		return (parseInt(incomes[1].income) / incomeSum.value * 100).toFixed(2) || null;
});

const expensesTotal = computed(() => {
	return expenses.filter(x => x.amount).map(a => parseInt(a.amount)).reduce((a, b) => a + b, 0);
});

const expensesPaidByJo = computed(() => {
	return expenses.filter(x => x.paidBy == "jo" && x.amount != null).map(a => parseInt(a.amount)).reduce((a, b) => a + b, 0) + (expensesPaidHalfAndHalf?.value / 2 || 0);
});

const expensesPaidByViki = computed(() => {
	return expenses.filter(x => x.paidBy == "viki" && x.amount != null).map(a => parseInt(a.amount)).reduce((a, b) => a + b, 0) + (expensesPaidHalfAndHalf?.value / 2 || 0);
});

const expensesPaidHalfAndHalf = computed(() => {
	return expenses.filter(x => x.paidBy == null && x.amount != null).map(a => parseInt(a.amount)).reduce((a, b) => a + b, 0);
});

const joTotal = computed(() => {
	return Math.round(expensesTotal.value * joPercentage.value / 100) || 0;
});

const vikiTotal = computed(() => {
	return Math.round(expensesTotal.value * vikPercentage.value / 100) || 0;
});

const joDifference = computed(() => {
	return joTotal.value - expensesPaidByJo.value;
});

const vikDifference = computed(() => {
	return vikiTotal.value - expensesPaidByViki.value;
});

const whoOwesWho = computed(() => {
	if (joDifference.value > vikDifference.value) {
		return "Jo le debe a Viki $" + Math.round(joDifference.value);
	}
	if (vikDifference.value > joDifference.value) {
		return "Viki le debe a Jo $" + Math.round(vikDifference.value);
	}
	if (joDifference.value == vikDifference.value) {
		return "Estamos a mano!"
	}
});

const incomesComplete = computed(() => {
	return incomes.every(x => x.income != null && x.income.length > 4);
});

const expensesComplete = computed(() => {
	return incomesComplete.value && expenses.some(x => x.amount != null && x.amount.length > 3);
});

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

const onClickSaveAll = () => {
	localStorage.setItem("savedExpenses", JSON.stringify(expenses));
	localStorage.setItem("sueldoJo", incomes[0].income);
	localStorage.setItem("sueldoViki", incomes[1].income);
}

const onlyNumbers = (event) => {
	let numbersRegex = /^\d+$/;
	if (!numbersRegex.test(event.key)) {
		event.preventDefault();
	}
}

const testEvent = (e) => {
	//console.log(e);
	//TODO -> Crear una prop para pasarle el porcentaje. Preguntar si conviene hacer esto, parece innecesario.
}
</script>

<style>
.container {
	width: 1000px;
	margin: 50px 0px;
	flex-direction: column;
}

@media (max-width: 1000px) {

	/* Si dejo esta media query aca, los selectores que se repitan abajo pueden pisar los estilos. Poner media queries al final. */
	.container {
		width: 100%;
		margin: 20px 0px;
		padding: 0px 10px;
	}

	.card {
		padding: 30px 20px 20px 20px;
	}

	.incomes-numbers,
	.expenses-results,
	.incomes-container {
		flex-direction: column;
	}

	.incomes-container {
		gap: 30px;
	}
}

.card {
	width: 100%;
	background: rgba(255, 255, 255, 1);
	/* background: rgba(var(--primary-color), 0.85); */
	padding: 30px;
	box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.3);
	border-radius: 5px;
}

.container,
.incomes-container,
.numbers-container,
.expense-container,
.incomes-numbers,
.expenses-results,
.expense-buttons,
.expenses {
	display: flex;
}

.incomes-container,
.numbers-container {
	justify-content: space-between;
}

.container,
.incomes-numbers,
.expenses-results,
.numbers-container,
.incomes-container {
	gap: 20px;
	/* Condicionar gap para que se aplique segun la width */
}

.expenses {
	flex-direction: column;
	gap: 15px;
}

.expense-container {
	/* gap: 10px; */
}

.income {
	flex-grow: 1;
}

.numbers-container>div,
.results-container>div {
	text-align: center;
}

i {
	color: silver;
}

.expense-buttons {
	/* border: 1px solid silver; */
	align-items: end;
	/* padding-bottom: 5px; */
}

.icon-container {
	/* don't take the whole line */
	display: inline-block;
	/* transition the background change */
	transition: background-color 0.25s;
	/* height of the icon */
	/* height: 1.5em; */
	/* width of the icon */
	width: 1.5em;
	/* space between the icon and the background edge */
	text-align: center;
	padding-top: 0.30em;
	/* make it rounded */
	/* border-radius: 50%; */
	border: 2px solid silver;
	border-left: none;
	height: 31px;
}

.icon-container:last-child {
	border-radius: 0px 3px 3px 0px;
}

.expense-container input {
	height: 31px;
	border-radius: 3px 0px 0px 3px;
}

.selected-paidby,
.icon-container:hover {
	/* add a background on hover */
	/* background-color: rgba(0, 0, 0, .10); */
	/* pointer cursor on hover */
	cursor: pointer;

}

.selected-paidby>i,
.icon-container:hover>i,
.black {
	color: black;
}

button {
	background: none;
	border: 2px solid silver;
	border-radius: 3px;
	padding: 7px 10px;
	color: silver;
	transition: color 0.25s, border 0.25s;
}

button>i {
	transition: color 0.25s;
}

button:hover {
	cursor: pointer;
	color: black;
	border: 2px solid black;
}

button:hover>i {
	color: black;
}

.card:has(.results-container),
.card:has(.numbers-container) {
	align-content: center;
}

.results-container {
	display: grid;
	grid-template-columns: repeat(2, 1fr);
	grid-gap: 10px;
	grid-auto-rows: minmax(100px, auto);
	align-items: center;
	justify-items: center;
	column-gap: 0px;
	row-gap: 0px;
}

.total {
	grid-column: 1 / 3;
	grid-row: 1;
}

.result {
	grid-column: 1 / 3;
	grid-row: 4;
}

.numbers-container>div>p,
.results-container>div>p {
	font-size: 14px;
	color: silver;
}

.numbers-container>div>h2,
.results-container>div>h2 {
	display: inline;
	vertical-align: middle;
}

.numbers-container>div>span,
.results-container>div>span {
	vertical-align: middle;
}

.add-expense-button-container {
	display: flex;
	justify-content: center;
	gap: 10px;
}

.incomes-placeholder,
.results-placeholder {
	height: 100%;
	display: flex;
	justify-content: center;
	align-items: center;
}

.expense-container:focus-within > .expense-buttons > .icon-container { 
    border-color: black;
}
</style>