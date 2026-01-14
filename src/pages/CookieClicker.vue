<script setup>
import { computed, onUnmounted, ref } from 'vue';

document.title = '🍪 Cookie Clicker';

let cookies = ref(0);
let totalClicks = ref(0);
let startTime = Date.now();
let hacked = ref(false);

let buildings = ref([
    { name: 'Cursor', price: 15, count: 0, cps: 0.1 },
    { name: 'Grandma', price: 100, count: 0, cps: 1 },
    { name: 'Farm', price: 1000, count: 0, cps: 8 },
    { name: 'Lab', price: 8000, count: 0, cps: 60 },
    { name: 'Factory', price: 40000, count: 0, cps: 400 },
    { name: 'Mine', price: 200000, count: 0, cps: 2000 },
    { name: 'Shipment', price: 1000000, count: 0, cps: 10000 },
]);

let cps = computed(() =>
    buildings.value.reduce((total, b) => total + b.cps * b.count, 0)
);

const cpsInterval = setInterval(() => {
    cookies.value += cps.value;
}, 1000);

function clickCookie() {
    totalClicks.value++;
    cookies.value += hacked.value ? 1_000_000 : 1;
}

function buyBuilding(building) {
    if (cookies.value >= building.price) {
        cookies.value -= building.price;
        building.price += Math.ceil(building.price * 0.15);
        building.count++;
    }
}

function getBuildingCount(name) {
    return buildings.value.find(b => b.name === name)?.count || 0;
}

const achievements = computed(() => [
    { label: '👆 Just Getting Started - Click the cookie once', achieved: totalClicks.value >= 1 },
    { label: '🍪 Crumbs Collector - Earn 100 cookies total', achieved: cookies.value >= 100 },
    { label: '🥠 Sweet Success - Earn 1,000 cookies', achieved: cookies.value >= 1000 },
    { label: '🏭 Small Business - Earn 10,000 cookies', achieved: cookies.value >= 10000 },
    { label: '💰 Cookie Tycoon - Earn 100,000 cookies', achieved: cookies.value >= 100000 },
    { label: '🤑 Millionaire Muncher - Reach 1,000,000 cookies', achieved: cookies.value >= 1000000 },
    { label: '💼 Billionaire Baker - Reach 1,000,000,000 cookies', achieved: cookies.value >= 1000000000 },
    { label: '👛 Trillionaire Treats - Reach 1,000,000,000,000 cookies', achieved: cookies.value >= 1000000000000 },
    { label: '👵 Grandma’s Pride - Own 10 Grandmas', achieved: getBuildingCount('Grandma') >= 10 },
    { label: '🌾 Farmville - Own 10 Farms', achieved: getBuildingCount('Farm') >= 10 },
    { label: '🚀 Out of This World - Buy a Shipment', achieved: getBuildingCount('Shipment') >= 1 },
    { label: '🧪 Mad Scientist - Buy a Lab', achieved: getBuildingCount('Lab') >= 1 },
    { label: '🎮 Afk King - Play for 1 hour', achieved: (Date.now() - startTime) >= 3600000 },
]);

onUnmounted(() => {
    clearInterval(cpsInterval);
});

const activeTab = ref('Stats');
function setTab(tab) {
    activeTab.value = tab;
}

function enableHacks() {
    hacked.value = true;
}

function resetGame() {
    location.reload();
}
</script>

<template>
    <div class="columns">
        <div class="column is-3 has-background-primary has-text-centered">
            <h1 class="is-size-1"><b>Cookie</b></h1>
            <figure class="image is-square m-5">
                <img @click="clickCookie" class="is-rounded"
                    src="https://pngimg.com/uploads/cookie/cookie_PNG13656.png" />
            </figure>
        </div>

        <div class="column is-9 has-background-info">
            <button class="tab_button button is-primary" :class="{ 'is-active': activeTab === 'Stats' }">
                <a @click="setTab('Stats')">📊Stats</a>
            </button>
            <button class="tab_button button is-primary" :class="{ 'is-active': activeTab === 'Achievements' }">
                <a @click="setTab('Achievements')">🥇Achievements</a>
            </button>
            <button class="tab_button button is-primary" :class="{ 'is-active': activeTab === 'Store' }">
                <a @click="setTab('Store')">🛒Store</a>
            </button>
            <button class="tab_button button is-primary" :class="{ 'is-active': activeTab === 'Settings' }">
                <a @click="setTab('Settings')">🤖Settings</a>
            </button>

            <div v-if="activeTab === 'Stats'">
                <h2 class="is-size-3">Stats</h2>
                <p>Total Cookies: {{ +cookies.toFixed(1) }}🍪</p>
                <p>Total CPS: {{ +cps.toFixed(1) }}</p>
                <ul>
                    <li v-for="b in buildings" :key="b.name">{{ b.name }}s: {{ b.count }}</li>
                </ul>
            </div>

            <div v-else-if="activeTab === 'Achievements'">
                <h2 class="is-size-3">Achievements</h2>
                <ul>
                    <li v-for="(a, i) in achievements" :key="i">
                        <input type="checkbox" disabled :checked="a.achieved" /> <b>{{ a.label }}</b>
                    </li>
                </ul>
            </div>

            <div v-else-if="activeTab === 'Store'">
                <h2 class="is-size-3">Store</h2>
                <p>Wallet: {{ +cookies.toFixed(1) }}🍪</p>
                <button class="button is-large is-fullwidth is-primary mb-2"
                    v-for="building in buildings"
                    :key="building.name"
                    :disabled="cookies < building.price"
                    @click="buyBuilding(building)">
                    {{ building.name }} - {{ building.price }}🍪
                </button>
            </div>

            <div v-else-if="activeTab === 'Settings'">
                <h2 class="is-size-3">Settings</h2>
                <button class="button is-warning mb-2" @click="enableHacks">Enable Hacks</button><br>
                <button class="button is-danger" @click="resetGame">Reset Game</button>
            </div>
        </div>
    </div>
</template>

<style scoped>
.columns {
    height: 100vh;
}
.tab_button {
    margin-right: 10px;
}
img:hover {
    cursor: pointer;
}
</style>