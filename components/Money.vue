<script setup>
const result = ref(Number(getItem("finiteResult")));
const moneys = ref();
function plus() {
    result.value += moneys.value;
    moneys.value = 0;
    setItem("finiteResult", result.value);
}
function minus() {
    result.value -= moneys.value;
    moneys.value = 0;
    setItem("finiteResult", result.value);
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
</script>

<template>
    <div
        class="bg-white px-40 py-20 flex flex-col gap-8 items-center justify-center rounded-2xl"
    >
        <h1 class="text-4xl font-bold">Анализ расходов</h1>
        <span class="text-xl text-stone-500 font-bold">{{ result }}</span>
        <div class="flex flex-col gap-10">
            <input
                type="number"
                class="rounded-xl border-2 hover:outline-stone-300 text-center text-stone-700 py-1"
                v-model="moneys"
            />
            <div class="flex gap-10">
                <button
                    class="bg-emerald-200 rounded-2xl px-6 py-2 text-stone-800 hover:scale-110 transition-all"
                    @click="plus"
                >
                    Добавить
                </button>
                <button
                    class="bg-rose-200 rounded-2xl px-7 py-2 text-stone-800 hover:scale-110 transition-alls"
                    @click="minus"
                >
                    Убавить
                </button>
            </div>
        </div>
    </div>
</template>
