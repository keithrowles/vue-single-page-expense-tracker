<template>
  <div>
    <HeaderNav />
    <div class="container">
      <Balance :total="+total" />
      <IncomeExpenses :income="+income" :expenses="+expenses" />
      <TransactionList
        :transactions="transactions"
        @transactiondeleted="handleTransactionDeleted"
      />
      <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
    </div>
  </div>
</template>

<script setup>
import HeaderNav from '@/components/HeaderNav.vue';
import Balance from '@/components/Balance.vue';
import IncomeExpenses from '@/components/IncomeExpenses.vue';
import TransactionList from '@/components/TransactionList.vue';
import AddTransaction from '@/components/AddTransaction.vue';
import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification';

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

// const transactions = ref([
//   { id: 1, text: 'Flower', amount: -20 },
//   { id: 2, text: 'Salary', amount: 300 },
//   { id: 3, text: 'Book', amount: -10 },
//   { id: 4, text: 'Camera', amount: 150 },
// ]);

// get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

// Get income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2);
});

// Get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2);
});

// Add transaction
const handleTransactionSubmitted = (data) => {
  // console.log(data);
  transactions.value.push({
    id: generateUniqueId(),
    text: data.text,
    amount: data.amount,
  });

  saveTransactionsToLocalStorage();

  toast.success('Transaction added');
};

// unique id
const generateUniqueId = () => {
  return Math.floor(Math.random() * 100000);
};

// Delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );

  saveTransactionsToLocalStorage();

  toast.success('Transaction Deleted');
};

// Save to local storage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
};
</script>

<style lang="scss" scoped></style>
