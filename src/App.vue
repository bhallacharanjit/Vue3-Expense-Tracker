<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income="+totalIncome" :expenses="+totalExpense" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <!--passing the prop-->
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>
<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";
import { ref, computed, onMounted } from "vue";
import { useToast } from "vue-toastification";

const toast = useToast();

onMounted(() => {
  const savedTransaction = JSON.parse(localStorage.getItem("transactions"));
  if (savedTransaction) {
    transactions.value = savedTransaction;
  }
});

const transactions = ref([]);

const total = computed(() => {
  return transactions.value.reduce((acc, item) => (acc += item.amount), 0);
});

const totalIncome = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, item) => (acc += item.amount), 0)
    .toFixed(2);
});

const totalExpense = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, item) => (acc += item.amount), 0)
    .toFixed(2);
});

//Transaction handler
const handleTransactionSubmitted = (transactionData) => {
  const id = generateUniqueId();
  console.log(id);
  transactions.value.push({
    id: id,
    text: transactionData.text,
    amount: transactionData.amount,
  });
  saveToLocalStorage();
  toast.success("Transaction added successfully");
};

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};

//Transaction Deleted
const handleTransactionDeleted = (id) => {
  console.log(id);
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );
  saveToLocalStorage();
  toast.success("Transaction deleted");
};

//save to Local Storage
const saveToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>
