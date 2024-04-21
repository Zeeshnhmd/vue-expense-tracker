<template>
	<Header />
	<div class="container">
		<Balance :total="+total" />
		<IncomeExpenses :income="+income" :expense="+expense" />
		<TransactionList
			:transactions="transactions"
			@transactionDeleted="handleTransactionDelete"
		/>
		<AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
	</div>
</template>

<script setup>
import { computed, ref, onMounted } from 'vue';
import { useToast } from 'vue-toastification';

import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
	const savedTransaction = JSON.parse(localStorage.getItem('transactions'));
	if (savedTransaction) {
		transactions.value = savedTransaction;
	}
});

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

	saveTransationToLocalStorage();

	toast.success('Transaction added');
};

// Delete Transaction
const handleTransactionDelete = (id) => {
	transactions.value = transactions.value.filter((el) => el.id !== id);

	saveTransationToLocalStorage();

	toast.success('Transaction deleted');
};

// Generate Unique ID
const generateUniqueId = () => {
	return Math.floor(Math.random() * 1000000);
};

// Save to Localstorage
const saveTransationToLocalStorage = () => {
	localStorage.setItem('transactions', JSON.stringify(transactions.value));
};
</script>
