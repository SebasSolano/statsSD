<template>
  <div class="flex justify-center p-10">
    <div class="bg-gray-800 p-8 rounded-lg shadow-lg max-w-4xl w-full">
      <!-- Botones flotantes -->
      <div class="fixed right-10 top-10 transform -translate-y-1/2 space-x-5">
        <button
          @click="exportData"
          class="bg-red-600 p-2 rounded-xl hover:bg-red-500 text-white w-28"
        >
          Exportar
        </button>
        <input
          type="file"
          id="importFile"
          ref="fileInput"
          @change="importData"
          hidden
        />
        <button
          @click="triggerFileInput"
          class="bg-green-600 p-2 rounded-xl hover:bg-green-500 text-white w-28"
        >
          Importar
        </button>
      </div>
      <!-- Sección de ingreso de nombre de usuario -->
      <div v-if="!userExists" class="mb-8">
        <h2 class="text-2xl font-bold mb-4">Ingresa tu nombre de usuario</h2>
        <input
          v-model="username"
          type="text"
          placeholder="Nombre de usuario"
          class="p-2 w-full rounded bg-gray-700 text-white"
        />
        <button
          @click="saveUsername"
          class="mt-4 w-full bg-blue-600 p-2 rounded hover:bg-blue-500"
        >
          Guardar
        </button>
      </div>

      <!-- Historial total -->
      <div v-if="userExists" class="mb-8">
        <h2 class="text-xl font-bold mb-4 text-white">Total History</h2>
        <div class="grid grid-cols-4 gap-4">
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>Total kills</strong> <span>{{ totalStats.kills }}</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>Total deaths:</strong>
            <span>{{ totalStats.deaths }}</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>Total assists:</strong>
            <span>{{ totalStats.assists }}</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>Total Damage:</strong> <span>{{ totalStats.damage }}</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>K/D:</strong> <span>{{ totalStats.kd }}</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>K/D/A:</strong> <span>{{ totalStats.kda }}</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>Win %:</strong> <span>{{ totalStats.winRate }}%</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>Average Damage:</strong>
            <span>{{ totalStats.damageRate }}</span>
          </div>
        </div>
      </div>
      <div class="p-1 bg-gray-700 rounded mb-5"></div>

      <!-- Historial diario -->
      <div v-if="userExists" class="mb-8">
        <h2 class="text-xl font-bold mb-4 text-white">Daily History</h2>
        <div class="grid grid-cols-3 gap-4">
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>Daily Kills:</strong> <span>{{ dailyStats.kills }}</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>Daily Deaths:</strong>
            <span>{{ dailyStats.deaths }}</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>Daily Assists:</strong>
            <span>{{ dailyStats.assists }}</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>Daily Damage:</strong> <span>{{ dailyStats.damage }}</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>K/D:</strong> <span>{{ dailyStats.kd }}</span>
          </div>
          <div
            class="flex flex-col items-start justify-start p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>K/D/A:</strong> <span>{{ dailyStats.kda }}</span>
          </div>
          <div
            class="flex items-center justify-start space-x-3 p-2 bg-gray-900 rounded-lg text-white hover:cursor-pointer hover:bg-gray-700"
          >
            <strong>Points Obtained:</strong>
            <span
              class="p-2 rounded"
              :class="dailyStats.points >= 0 ? 'bg-green-500' : 'bg-red-500'"
              >{{ dailyStats.points }}</span
            >
          </div>
        </div>
      </div>
      <div class="p-1 bg-gray-700 rounded mb-5"></div>
      <!-- Inputs para ingresar datos diarios -->
      <div v-if="userExists" class="mb-8">
        <h2 class="text-xl font-bold mb-4 text-white">Enter Daily Data</h2>
        <form class="grid grid-cols-3 gap-4">
          <div class="flex flex-col">
            <label class="text-white">Kills:</label>
            <input
              v-model.number="inputStats.kills"
              type="number"
              class="p-2 rounded bg-gray-700 text-white"
            />
          </div>
          <div class="flex flex-col">
            <label class="text-white">Deaths:</label>
            <input
              v-model.number="inputStats.deaths"
              type="number"
              class="p-2 rounded bg-gray-700 text-white"
            />
          </div>
          <div class="flex flex-col">
            <label class="text-white">Assists:</label>
            <input
              v-model.number="inputStats.assists"
              type="number"
              class="p-2 rounded bg-gray-700 text-white"
            />
          </div>
          <div class="flex flex-col">
            <label class="text-white">Damage:</label>
            <input
              v-model.number="inputStats.damage"
              type="number"
              class="p-2 rounded bg-gray-700 text-white"
            />
          </div>
          <div class="flex flex-col">
            <label class="text-white">Points:</label>
            <input
              v-model.number="inputStats.points"
              type="number"
              class="p-2 rounded bg-gray-700 text-white"
            />
          </div>
        </form>
        <button
          type="submit"
          :click="saveDailyStats"
          class="col-span-2 bg-blue-600 p-2 rounded-full hover:bg-blue-500 text-white text-xl w-full mt-3"
        >
          Save
        </button>
      </div>
      <div class="p-1 bg-gray-700 rounded mb-5"></div>
      <!-- Historial de ranks en formato de cards -->
      <div v-if="userExists">
        <h2 class="text-xl font-bold mb-4 text-white">Rank History</h2>
        <div class="space-y-4 overflow-y-auto max-h-96">
          <div
            v-for="rank in rankHistory"
            :key="rank.date"
            :class="rank.points >= 0 ? 'bg-green-500' : 'bg-red-500'"
            class="flex items-start justify-start bg-gray-700 space-x-3 p-4 rounded-lg shadow-lg hover:cursor-pointer"
          >
            <div class="p-5 bg-slate-300 rounded-full"></div>
            <div class="flex flex-col items-start justify-start">
              <div>
                {{ rank.points }}
                <strong class="ml-1">KDA</strong> {{ rank.kills }}/{{
                  rank.deaths
                }}/{{ rank.assists }}
              </div>
              <div class=""><strong>Damage:</strong> {{ rank.damage }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
  import { ref, onMounted } from "vue";

  const username = ref("");
  const userExists = ref(false);
  const totalStats = ref({
    kills: 0,
    deaths: 0,
    assists: 0,
    damage: 0,
    kd: 0,
    kda: 0,
    winRate: 0,
    damageRate: 0,
  });
  const dailyStats = ref({
    kills: 0,
    deaths: 0,
    assists: 0,
    damage: 0,
    kd: 0,
    kda: 0,
    points: 0,
  });
  const inputStats = ref({
    kills: 0,
    deaths: 0,
    assists: 0,
    damage: 0,
    points: 0,
  });
  const rankHistory = ref([]);
  let db = null;
  const fileInput = ref(null);

  const initDB = () => {
    const request = indexedDB.open("ranksDB", 1);
    request.onupgradeneeded = (event) => {
      db = event.target.result;
      db.createObjectStore("ranks", { keyPath: "date" });
      db.createObjectStore("user", { keyPath: "id" });
    };
    request.onsuccess = (event) => {
      db = event.target.result;
      checkUser();
    };
  };

  const checkUser = () => {
    const transaction = db.transaction("user", "readonly");
    const store = transaction.objectStore("user");
    const request = store.get(1);
    request.onsuccess = (event) => {
      if (event.target.result) {
        username.value = event.target.result.name;
        userExists.value = true;
        loadRanks();
      }
    };
  };

  const saveUsername = () => {
    const transaction = db.transaction("user", "readwrite");
    const store = transaction.objectStore("user");
    store.put({ id: 1, name: username.value });
    userExists.value = true;
  };

  const saveDailyStats = () => {
    const rank = {
      date: new Date().toISOString(),
      ...inputStats.value,
    };
    const transaction = db.transaction("ranks", "readwrite");
    const store = transaction.objectStore("ranks");
    store.add(rank);
    loadRanks();
  };

  const loadRanks = () => {
    const transaction = db.transaction("ranks", "readonly");
    const store = transaction.objectStore("ranks");
    const request = store.getAll();
    request.onsuccess = (event) => {
      const ranks = event.target.result;
      rankHistory.value = ranks.reverse();
      calculateDailyStats(ranks);
      calculateTotalStats(ranks);
    };
  };

  const calculateDailyStats = (ranks) => {
    const today = new Date().toISOString().split("T")[0];
    const dailyRanks = ranks.filter(
      (rank) => rank.date.split("T")[0] === today
    );

    let totalKills = 0,
      totalDeaths = 0,
      totalAssists = 0,
      totalDamage = 0,
      totalPoints = 0;

    dailyRanks.forEach((rank) => {
      totalKills += rank.kills;
      totalDeaths += rank.deaths;
      totalAssists += rank.assists;
      totalDamage += rank.damage;
      totalPoints += rank.points;
    });

    dailyStats.value.kills = totalKills;
    dailyStats.value.deaths = totalDeaths;
    dailyStats.value.assists = totalAssists;
    dailyStats.value.damage = totalDamage;
    dailyStats.value.kd =
      totalDeaths > 0
        ? (totalKills / totalDeaths).toFixed(2)
        : totalKills.toFixed(2);
    dailyStats.value.kda = (totalKills + totalAssists / totalDeaths).toFixed(2);
    dailyStats.value.points = totalPoints;
  };

  const calculateTotalStats = (ranks) => {
    let totalKills = 0,
      totalDeaths = 0,
      totalAssists = 0,
      totalDamage = 0,
      totalPoints = 0;
    let wins = 0;

    ranks.forEach((rank) => {
      totalKills += rank.kills;
      totalDeaths += rank.deaths;
      totalAssists += rank.assists;
      totalDamage += rank.damage;
      totalPoints += rank.points;
      if (rank.points > 0) {
        wins++;
      }
    });

    totalStats.value.kills = totalKills;
    totalStats.value.deaths = totalDeaths;
    totalStats.value.assists = totalAssists;
    totalStats.value.damage = totalDamage;
    totalStats.value.kd =
      totalDeaths > 0
        ? (totalKills / totalDeaths).toFixed(2)
        : totalKills.toFixed(2);
    totalStats.value.kda = (totalKills + totalAssists / totalDeaths).toFixed(2);
    totalStats.value.winRate = ((wins / ranks.length) * 100).toFixed(2);
    totalStats.value.damageRate = (totalDamage / ranks.length).toFixed(2);
  };

  const exportData = () => {
    const transaction = db.transaction(["ranks", "user"], "readonly");
    const ranksStore = transaction.objectStore("ranks");
    const userStore = transaction.objectStore("user");

    const ranksRequest = ranksStore.getAll();
    const userRequest = userStore.getAll();

    ranksRequest.onsuccess = () => {
      userRequest.onsuccess = () => {
        const data = {
          ranks: ranksRequest.result,
          user: userRequest.result[0],
        };
        const blob = new Blob([JSON.stringify(data, null, 2)], {
          type: "application/json",
        });
        const url = URL.createObjectURL(blob);
        const a = document.createElement("a");
        a.href = url;
        a.download = "rank_data.json";
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
      };
    };
  };

  const triggerFileInput = () => {
    fileInput.value.click();
  };

  const importData = (event) => {
    const file = event.target.files[0];
    if (!file) return;

    const reader = new FileReader();
    reader.onload = (e) => {
      const importedData = JSON.parse(e.target.result);
      if (importedData && importedData.ranks && importedData.user) {
        const transaction = db.transaction(["ranks", "user"], "readwrite");
        const ranksStore = transaction.objectStore("ranks");
        const userStore = transaction.objectStore("user");

        // Importar datos de usuario
        userStore.put(importedData.user);

        // Importar datos de ranks
        importedData.ranks.forEach((rank) => {
          ranksStore.put(rank);
        });

        loadRanks();
      }
    };
    reader.readAsText(file);
  };
  onMounted(() => {
    initDB();
  });
</script>

<style scoped></style>
