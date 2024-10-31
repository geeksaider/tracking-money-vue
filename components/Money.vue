<script setup>
import { data } from "autoprefixer";

const moneys = ref(0);
const types = ref("");

const expenses = ref(0);
const income = ref(0);

const result = ref(Number(getItem("finiteResult")));
const historyData = ref([]);

onMounted(() => {
    if (getItem("dateProject") != null) {
        let historyDataStorage = JSON.parse(getItem("dateProject"));
        historyDataStorage = historyDataStorage.map((h) => {
            h.date = new Date(Date.parse(h.date));
            return h;
        });
        for (let i = 0; i < historyDataStorage.length; i++) {
            historyData.value.push(historyDataStorage[i]);
        }
    }
    if (getItem("transaction") != null) {
        let arrTemp = JSON.parse(getItem("transaction"));
        const [expensesTemp, incomeTemp] = arrTemp;
        expenses.value = expensesTemp;
        income.value = incomeTemp;
    }
});

function update(e) {
    console.log(e.target.value);
    if (e.target.value == "plus" && moneys.value != "" && types.value != "") {
        result.value += moneys.value;
    } else if (
        e.target.value == "minus" &&
        moneys.value != "" &&
        types.value != ""
    ) {
        result.value -= moneys.value;
        moneys.value = moneys.value * -1;
        console.log("z nen");
    } else {
        return alert("Поле цены или типа пустое");
    }

    setItem("finiteResult", result.value);

    historyData.value.push({
        type: types.value,
        cost: moneys.value,
        date: new Date(),
    });

    countExpenessAndIncome();

    moneys.value = "";
    types.value = "";

    setItem("dateProject", JSON.stringify(historyData.value));
}

function countExpenessAndIncome() {
    income.value = historyData.value.reduce((incomeSum, incomeValue) => {
        if (incomeValue.cost >= 0 && incomeValue.cost != "") {
            console.log("" >= 0);
            return incomeSum + incomeValue.cost;
        }
        return incomeSum;
    }, 0);

    expenses.value = historyData.value.reduce((expensesSum, expensesValue) => {
        if (expensesValue.cost < 0) {
            return expensesSum + expensesValue.cost;
        }
        return expensesSum;
    }, 0);

    expenses.value *= -1;

    let arrForTransaction = [expenses.value, income.value];

    setItem("transaction", JSON.stringify(arrForTransaction));
}

function getItem(item) {
    if (process.client) {
        return localStorage.getItem(item);
    } else {
        return undefined;
    }
}

function setItem(item, value) {
    if (process.client) {
        localStorage.setItem(item, value);
        return true;
    } else {
        return false;
    }
}

function formatDate(date) {
    const monthArr = [
        "Янв",
        "Фев",
        "Мар",
        "Апр",
        "Май",
        "Июнь",
        "Июль",
        "Авг",
        "Сен",
        "Окт",
        "Нояб",
        "Декаб",
    ];

    const beautifulDateString =
        date.getDay() +
        " ое " +
        monthArr[date.getMonth()] +
        "  " +
        date.getHours() +
        ":" +
        date.getMinutes();
    return beautifulDateString;
}

function deliteArr(now, cost) {
    result.value -= cost;
    setItem("finiteResult", result.value);
    historyData.value = historyData.value.filter((h) => h.date != now);
    countExpenessAndIncome();
    setItem("dateProject", JSON.stringify(historyData.value));
}
</script>

<template>
    <div
        class="bg-white px-40 py-20 flex flex-col gap-8 items-center justify-center rounded-2xl my-20"
    >
        <h1 class="text-4xl font-bold">Общий баланс</h1>
        <span class="text-2xl text-stone-700 font-bold">{{ result }} ₽</span>
        <div class="flex gap-20">
            <div class="flex flex-col items-center gap-2">
                <h1 class="text-emerald-500 font-bold text-xl">Доходы</h1>
                <span class="text-lg font-semibold text-emerald-400">{{
                    income
                }}</span>
            </div>
            <div class="flex flex-col items-center gap-2">
                <h1 class="text-rose-500 font-bold text-xl">Расходы</h1>
                <span class="text-lg text-rose-400 font-semibold">{{
                    expenses
                }}</span>
            </div>
        </div>
        <div class="flex flex-col gap-10 items-center">
            <div class="flex flex-row justify-between">
                <input
                    type="text"
                    class="rounded-xl border-2 hover:outline-stone-300 text-center text-stone-700 py-1 basis-3/5 min-w-0 mr-4"
                    v-model="types"
                />
                <input
                    type="number"
                    class="rounded-xl border-2 hover:outline-stone-300 text-center text-stone-700 py-1 basis-2/5 min-w-0"
                    v-model="moneys"
                />
            </div>
            <div class="flex gap-10 mb-3">
                <button
                    class="bg-emerald-200 rounded-2xl px-6 py-2 text-stone-800 hover:scale-110 transition-all"
                    value="plus"
                    @click="update"
                >
                    Добавить
                </button>
                <button
                    class="bg-rose-200 rounded-2xl px-7 py-2 text-stone-800 hover:scale-110 transition-alls"
                    value="minus"
                    @click="update"
                >
                    Убавить
                </button>
            </div>
            <div class="flex flex-col w-full gap-8">
                <div
                    v-for="object in historyData"
                    class="flex justify-between items-center w-full"
                >
                    <p class="text-lg font-bold">{{ object.type }}</p>
                    <div
                        class="flex justify-between gap-4 text-stone-500 items-center font-bold"
                    >
                        <span>{{ object.cost }} ₽</span>
                        <span>{{ formatDate(object.date) }}</span>
                        <button
                            class="bg-rose-200 rounded-2xl px-2 py-1 text-stone-500 hover:scale-110 transition-all"
                            @click="() => deliteArr(object.date, object.cost)"
                        >
                            Удалить
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
