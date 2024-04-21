<template>
	<Header />
	<div class="container">
		<Balance :total="+total" />
		<IncomeExpenses :income="+income" :expense="+expense" />
		<TransactionList :transactions="transactions" />
		<AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
	</div>
</template>

<script setup>
import { computed, ref } from 'vue';
import { useToast } from 'vue-toastification';

import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

const toast = useToast();

const transactions = ref([
	{ id: 1, text: 'Flower', amount: -19.99 },
	{ id: 2, text: 'Salary', amount: 299.97 },
	{ id: 3, text: 'Book', amount: -10 },
	{ id: 4, text: 'Camera', amount: 150 },
]);

// Get Total
const total = computed(() => {
	return transactions.value.reduce((acc, transaction) => {
		return acc + transaction.amount;
	}, 0);
});

// Get Income
const income = computed(() => {
	return transactions.value
		.filter((el) => el.amount > 0)
		.reduce((acc, transaction) => {
			return acc + transaction.amount;
		}, 0)
		.toFixed(2);
});

// Get expenses
const expense = computed(() => {
	return transactions.value
		.filter((el) => el.amount < 0)
		.reduce((acc, transaction) => {
			return acc + transaction.amount;
		}, 0)
		.toFixed(2);
});

// Add Transaction
const handleTransactionSubmitted = (transactionData) => {
	transactions.value.push({
		id: generateUniqueId(),
		text: transactionData.text,
		amount: transactionData.amount,
	});

	toast.success('Transaction added');
};

// Generate Unique ID
const generateUniqueId = () => {
	return Math.floor(Math.random() * 1000000);
};
</script>
