
<template>
  <Header/>
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income = "+income" :expenses="+expenses"/>
    <TransactionList  :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
  </div>
</template>

<script setup >
  import Header from './components/Header.vue';
  import Balance from './components/Balance.vue';
  import IncomeExpenses from './components/IncomeExpenses.vue';
  
  import TransactionList from './components/TransactionList.vue';
  import AddTransaction from './components/AddTransaction.vue';
  import {ref,computed,onMounted } from 'vue';
  import {useToast} from 'vue-toastification'

const toast = useToast()
onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));
  if (savedTransactions){
    transactions.value = savedTransactions;
  }
})
const transactions= ref([
 

  ]);
 
// get total 
const total = computed(() =>{
  return transactions.value.reduce((acc,transaction )=>{
    return acc + transaction.amount;
  },0).toFixed(2);
});
// get income 

const income = computed(() =>{
  return transactions.value
  .filter((transaction) => transaction.amount > 0)
  .reduce((acc,transaction )=>{
    return acc + transaction.amount;
  },0).toFixed(2);
});


// get expenses 
const expenses = computed(() =>{
  return transactions.value
  .filter((transaction) => transaction.amount < 0)
  .reduce((acc,transaction )=>{
    return acc + transaction.amount;
  },0).toFixed(2);
});
// adding transaction
const handleTransactionSubmitted = (transactionData) => {
  if (transactions.value.length > 0) {
  transactions.value.push({
    id: ((transactions.value.slice(-1)[0].id)+1),
    text:transactionData.text,
    amount : transactionData.amount


  });
  }   
  else {
    transactions.value.push({
    id : 1,
    text:transactionData.text,
    amount : transactionData.amount
  });
}
  saveTransactionsToLocalStorage();
  console.log(transactions.value);
  toast.success('Transaction added');
}
// deleting transaction
const handleTransactionDeleted = (id) => {
  transactions.value =transactions.value.filter((transaction) => transaction.id!==id)
  toast.success("Transaction deleted");
  saveTransactionsToLocalStorage();
}
//save to local sotrage
const saveTransactionsToLocalStorage =() => {
  localStorage.setItem('transactions',JSON.stringify(transactions.value));
}

</script>


