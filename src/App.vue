<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpense :income="+income" :expense="+expense" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <!-- <AddTransaction @transactionSubmitted="handleTransactionSubmitted" /> -->
    <button @click="openModal" class="btn">Open Modal</button>
    <Modal
      :isOpen="isModalOpened"
      @modal-close="closeModal"
      @submit="closeModal"
      name="modal"
    >
      <template #header>
        <h4>Add Transaction</h4>
        <button class="close-btn" @click="closeModal">X</button>
      </template>
      <template #content>
        <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
      </template>
    </Modal>
  </div>
</template>

<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpense from "./components/IncomeExpense.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";
import Modal from "./components/Modal.vue";

import { useToast } from "vue-toastification";

const toast = useToast();

import { computed, ref, onMounted } from "vue";

// Modal Setting
const isModalOpened = ref(false);
const openModal = () => {
  isModalOpened.value = true;
};

const closeModal = () => {
  isModalOpened.value = false;
};

onMounted(() => {
  const savedTransaction = JSON.parse(localStorage.getItem("transactions"));
  if (savedTransaction) {
    transactions.value = savedTransaction;
  }
});
const transactions = ref([]);

const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

// Get Income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

// Get Expense
const expense = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

// add Transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });

  savedToLocalStorage();

  toast.success("Transaction Added");
};

// Generate Id
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};

// handle Delete
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id != id
  );
  savedToLocalStorage();
  toast.success("Transaction Deleted");
};

// Saved to local storage
const savedToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>
