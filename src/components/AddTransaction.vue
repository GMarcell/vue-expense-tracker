<template>
  <h3>Add New Transaction</h3>
  <form id="form" @submit.prevent="onSubmit">
    <div class="form-control">
      <label for="text">Text</label>
      <input type="text" id="text" v-model="text" placeholder="Enter Text..." />
    </div>
    <div class="transaction-type">
      Transaction Type
      <div class="form-control">
        <input type="radio" value="income" v-model="transactionType" />
        <label for="type">Income</label>
        <input type="radio" value="expense" v-model="transactionType" />
        <label for="type">Expense</label>
      </div>
    </div>
    <div class="form-control">
      <label for="amount">Amount</label>
      <input
        type="number"
        id="amount"
        v-model="amount"
        placeholder="Enter Amount..."
      />
    </div>
    <button class="btn">Add Transaction</button>
  </form>
</template>

<script setup>
import { ref } from "vue";
import { useToast } from "vue-toastification";

const text = ref("");
const amount = ref("");
const transactionType = ref("income");

const emit = defineEmits(["transactionSubmitted"]);

const toast = useToast();

const onSubmit = () => {
  if (!text.value || !amount.value) {
    toast.error("Both Fields must be Filled");
    return;
  }
  const transactionData = {
    text: text.value,
    amount:
      transactionType == "income"
        ? parseFloat(amount.value)
        : 0 - parseFloat(amount.value),
  };

  emit("transactionSubmitted", transactionData);

  text.value = "";
  amount.value = "";
};
</script>

<style scoped>
.transaction-type {
  display: flex;
  flex-direction: column;
  padding-top: 25px;
}
</style>
