<template>
  <h3>History</h3>
  <div v-if="transactions.length < 1">No Transaction Recorded</div>
  <ul class="list" id="list" v-if="transactions.length > 0">
    <li
      v-for="t in transactions"
      :key="t.id"
      :class="t.amount < 0 ? 'minus' : 'plus'"
    >
      {{ t.text }} <span>${{ t.amount }}</span
      ><button @click="deleteTransaction(t.id)" class="delete-btn">X</button>
    </li>
  </ul>
</template>

<script setup>
const emit = defineEmits(["transactionDeleted"]);

const props = defineProps({
  transactions: {
    type: Array,
    required: true,
  },
});

const deleteTransaction = (id) => {
  emit("transactionDeleted", id);
};
</script>
