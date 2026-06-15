<script setup>
import { ref, computed, watch } from 'vue';

const transactions = ref([]);
const filterType = ref('all');
const budgetLimit = ref(1000);

const desc = ref('');
const amount = ref(null);
const type = ref('Choose...');

const filterTransaction = computed(() => {
  if (filterType.value === 'all') {
    return transactions.value
  } else {
    return transactions.value.filter(transaction => {
      return transaction.type === filterType.value
    })
  }
})

const totalIncome = computed(() => {
  let total = 0;
  transactions.value.forEach(transaction => {
    if (transaction.type === 'income') {
      total += transaction.amount;
    }
  });
  return total;
})

const totalExpense = computed(() => {
  let total = 0;
  transactions.value.forEach(transaction => {
    if (transaction.type === 'expense') {
      total += transaction.amount;
    }
  });
  return total;
})

const balance = computed(() => {
  return totalIncome.value - totalExpense.value;
})

const isOverBudget = computed(() => {
  return totalExpense.value > budgetLimit.value;
})

const expensePercentage = computed(() => {
  if (budgetLimit.value <= 0) return 0;
  let percentage = (totalExpense.value / budgetLimit.value) * 100;
  return percentage > 100 ? 100 : Math.round(percentage); 
})

const categorySummary = computed(() => {
  let summary = {};
  transactions.value.forEach(transaction => {
    if (transaction.type === 'expense') {
      if (!summary[transaction.desc]) {
        summary[transaction.desc] = 0;
      }
      summary[transaction.desc] += transaction.amount;
    }
  });
  return summary; // Returns an object like: { skincar: 80 }
})

const budgetStatus = computed(() => {
  if (isOverBudget.value) {
    return 'Over Budget!';
  } else {
    return 'Within Budget';
  }
})

function addTransaction() {
  let transaction = { 'id': new Date().getTime(), 'desc': desc.value, 'amount': Number(amount.value), 'type': type.value, 'date': new Date().toLocaleDateString() };
  transactions.value.push(transaction);
  
  // Clear inputs after submission
  desc.value = '';
  amount.value = null;
  type.value = 'Choose...';
}

function deleteTransaction(id) {
  transactions.value = transactions.value.filter(transaction => transaction.id !== id);
}

function clearAll() {
  transactions.value = [];
}

// Watcher 1: Deep watch transactions to save to localStorage
watch(transactions, (newTransactions) => {
  localStorage.setItem('transactions', JSON.stringify(newTransactions));
}, { deep: true });

// Watcher 2: Warning if balance falls below 0
watch(balance, (newBalance) => {
  if (newBalance < 0) {
    alert('Warning: Balance is below 0!');
  }
});

// Watcher 3: Notification Log if budget limit is crossed
watch(isOverBudget, (newValue) => {
  if (newValue) {
    console.log('Notification Log: Budget limit has been exceeded!');
  }
});

</script>

<template>
  <h2 class="mb-4 text-center fw-bold">Personal Finance Tracker</h2>

  <div class="container mb-4 d-flex gap-3">
    <div class="card flex-grow-1 text-bg-primary mb-3">
      <div class="card-header">Balance</div>
      <div class="card-body">
        <h5 class="card-title">{{ balance }}</h5>
      </div>
    </div>
    <div class="card flex-grow-1 text-bg-success mb-3">
      <div class="card-header">Income</div>
      <div class="card-body">
        <h5 class="card-title">{{ totalIncome }}</h5>
      </div>
    </div>
    <div class="card flex-grow-1 text-bg-danger mb-3">
      <div class="card-header">Expense</div>
      <div class="card-body">
        <h5 class="card-title">{{ totalExpense }}</h5>
      </div>
    </div>
  </div>

  <div class="container mb-4">
    <div class="card p-3">
      <div class="d-flex justify-content-between align-items-center mb-2">
        <div>
          <span>Budget Status: </span>
          <span :class="isOverBudget ? 'text-danger fw-bold' : 'text-success'">
            {{ budgetStatus }}
          </span>
        </div>
        <div class="d-flex align-items-center gap-2">
          <label for="limit" class="form-label mb-0 text-nowrap">Limit ($):</label>
          <input id="limit" type="number" v-model.number="budgetLimit" class="form-control form-control-sm"
            style="width: 100px;">
        </div>
      </div>

      <div class="progress" style="height: 25px;">
        <div class="progress-bar progress-bar-striped progress-bar-animated transition"
          :class="isOverBudget ? 'bg-danger' : 'bg-warning text-dark'" role="progressbar"
          :style="{ width: expensePercentage + '%' }" :aria-valuenow="expensePercentage" aria-valuemin="0"
          aria-valuemax="100">
          {{ expensePercentage }}% Used
        </div>
      </div>
    </div>
  </div>

  <div class="container mb-4">
    <div class="card p-3">
      <h6 class="fw-bold">Category Summary (Expenses)</h6>
      <ul class="list-unstyled mb-0">
        <li v-for="(total, category) in categorySummary" :key="category">
          <span class="text-capitalize">{{ category }}:</span> ${{ total }}
        </li>
        <li v-if="Object.keys(categorySummary).length === 0" class="text-muted small">No expenses yet.</li>
      </ul>
    </div>
  </div>

  <div class="container mb-4">
    <form @submit.prevent="addTransaction">

      <div class="row g-3 align-items-center">

        <div class="col-sm-4">
          <label for="Description">Description</label>
          <input v-model="desc" type="text" class="form-control" id="Description">
        </div>

        <div class="col-sm-3">
          <label for="Amount">Amount</label>
          <input v-model="amount" type="number" class="form-control" id="Amount">
        </div>

        <div class="col-sm-3">
          <label for="type">Type</label>
          <select v-model="type" class="form-select" id="type">
            <option disabled value="Choose...">Choose...</option>
            <option value="income">Income</option>
            <option value="expense">Expense</option>
          </select>
        </div>

        <div class="col-sm-2">
          <label class="d-block">&nbsp;</label> <button type="submit" class="btn btn-primary w-100">Submit</button>
        </div>

      </div>
    </form>
  </div>

  <div class="mb-4">
    <div class="container d-flex gap-3 mb-3">
      <button type="button" class="btn btn-primary" @click="filterType = 'all'">All transaction</button>
      <button type="button" class="btn btn-success" @click="filterType = 'income'">Income transaction</button>
      <button type="button" class="btn btn-danger" @click="filterType = 'expense'">Expense transaction</button>

      <button type="button" class="btn btn-outline-danger ms-auto" @click="clearAll">Clear all transactions</button>
    </div>

    <div class="container">
      <table class="table">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Description</th>
            <th scope="col">Amount</th>
            <th scope="col">Type</th>
            <th scope="col">Date</th>
            <th scope="col">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="transaction in filterTransaction" :key="transaction.id">
            <td>{{ transaction.id }}</td>
            <td>{{ transaction.desc }}</td>
            <td>{{ transaction.amount }}</td>
            <td>{{ transaction.type }}</td>
            <td>{{ transaction.date }}</td>
            <td><button type="button" class="btn btn-danger" @click="deleteTransaction(transaction.id)">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>