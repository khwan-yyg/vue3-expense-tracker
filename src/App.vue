<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpense :income="+income" :expenses="+expenses" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpense from "./components/IncomeExpense.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";

import { useToast } from "vue-toastification";

import { ref, computed, onMounted } from "vue";

const toast = useToast();

const transactions = ref([
  {
    id: 452365,
    text: "Flower",
    amount: -19.99,
  },
  {
    id: 398517,
    text: "Salary",
    amount: 299.97,
  },
  {
    id: 517339,
    text: "Book",
    amount: -10,
  },
  {
    id: 728827,
    text: "Mom",
    amount: 150,
  },
]);

onMounted(() => {
  const saveTransactions = JSON.parse(localStorage.getItem("transactions"));

  if (saveTransactions) {
    transactions.value = saveTransactions;
  }
});

// Get total
const total = computed(() => {
  // Start zero then loop for add amount
  return transactions.value
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

// Get income
const income = computed(() => {
  // Start zero filter income
  // Then loop for add amount with decimal
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

// Get expense
const expenses = computed(() => {
  // Start zero filter expenses
  // Then loop for add amount with decimal
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

// Generate unique id
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};

// Save to localStorage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};

// Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });

  saveTransactionsToLocalStorage();

  toast.success("Transaction added");
};

// Delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );

  saveTransactionsToLocalStorage();

  toast.success("Transaction deleted");
};
</script>
