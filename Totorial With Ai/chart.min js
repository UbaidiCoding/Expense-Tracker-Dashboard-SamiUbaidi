// Selectors
const form = document.getElementById("transaction-form");
const titleInput = document.getElementById("title");
const amountInput = document.getElementById("amount");
const dateInput = document.getElementById("date");
const categoryInput = document.getElementById("category");
const list = document.getElementById("transaction-list");

const totalIncomeEl = document.getElementById("total-income");
const totalExpenseEl = document.getElementById("total-expense");
const balanceEl = document.getElementById("balance");

const filterCategory = document.getElementById("filter-category");
const filterDate = document.getElementById("filter-date");

const pieCtx = document.getElementById("pie-chart").getContext("2d");
const barCtx = document.getElementById("bar-chart").getContext("2d");

// Initialize data
let transactions = JSON.parse(localStorage.getItem("transactions")) || [];

// Chart instances
let pieChart, barChart;

// Save and update localStorage
function saveTransactions() {
  localStorage.setItem("transactions", JSON.stringify(transactions));
}

// Render Transactions
function renderTransactions(data = transactions) {
  list.innerHTML = "";
  data.forEach((tx, index) => {
    const li = document.createElement("li");
    li.classList.add(tx.category);
    li.innerHTML = `
      <span>${tx.title} (${tx.date})</span>
      <span>$${tx.amount.toFixed(2)}</span>
    `;
    list.appendChild(li);
  });
}

// Update Summary
function updateSummary() {
  const income = transactions
    .filter(tx => tx.category === "income")
    .reduce((sum, tx) => sum + tx.amount, 0);
  const expense = transactions
    .filter(tx => tx.category === "expense")
    .reduce((sum, tx) => sum + tx.amount, 0);
  const balance = income - expense;

  totalIncomeEl.textContent = `$${income.toFixed(2)}`;
  totalExpenseEl.textContent = `$${expense.toFixed(2)}`;
  balanceEl.textContent = `$${balance.toFixed(2)}`;
}

// Chart Updates
function updateCharts() {
  const income = transactions
    .filter(tx => tx.category === "income")
    .reduce((sum, tx) => sum + tx.amount, 0);
  const expense = transactions
    .filter(tx => tx.category === "expense")
    .reduce((sum, tx) => sum + tx.amount, 0);

  // Pie Chart
  if (pieChart) pieChart.destroy();
  pieChart = new Chart(pieCtx, {
    type: "pie",
    data: {
      labels: ["Income", "Expense"],
      datasets: [{
        data: [income, expense],
        backgroundColor: ["#00ffab", "#ff4c60"],
      }]
    }
  });

  // Bar Chart by Month
  const months = {};
  transactions.forEach(tx => {
    const month = tx.date.slice(0, 7);
    months[month] = months[month] || { income: 0, expense: 0 };
    months[month][tx.category] += tx.amount;
  });

  const labels = Object.keys(months);
  const incomeData = labels.map(m => months[m].income || 0);
  const expenseData = labels.map(m => months[m].expense || 0);

  if (barChart) barChart.destroy();
  barChart = new Chart(barCtx, {
    type: "bar",
    data: {
      labels: labels,
      datasets: [
        {
          label: "Income",
          data: incomeData,
          backgroundColor: "#00ffab"
        },
        {
          label: "Expense",
          data: expenseData,
          backgroundColor: "#ff4c60"
        }
      ]
    },
    options: {
      responsive: true,
      plugins: {
        legend: {
          labels: {
            color: '#000'
          }
        }
      },
      scales: {
        x: { ticks: { color: '#000' } },
        y: { ticks: { color: '#000' } }
      }
    }
  });
}

// Filter Transactions
function filterTransactions() {
  let filtered = [...transactions];

  const category = filterCategory.value;
  const month = filterDate.value;

  if (category !== "all") {
    filtered = filtered.filter(tx => tx.category === category);
  }

  if (month) {
    filtered = filtered.filter(tx => tx.date.startsWith(month));
  }

  renderTransactions(filtered);
}

// Add New Transaction
form.addEventListener("submit", (e) => {
  e.preventDefault();

  const title = titleInput.value.trim();
  const amount = parseFloat(amountInput.value);
  const date = dateInput.value;
  const category = categoryInput.value;

  if (!title || !amount || !date) return alert("Please fill all fields");

  transactions.push({ title, amount, date, category });
  saveTransactions();
  updateSummary();
  updateCharts();
  renderTransactions();
  filterTransactions();

  form.reset();
});

// Filter listeners
filterCategory.addEventListener("change", filterTransactions);
filterDate.addEventListener("change", filterTransactions);

// Initial Render
updateSummary();
renderTransactions();
updateCharts();
