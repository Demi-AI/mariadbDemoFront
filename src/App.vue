<template>
  <div class="main-container">

    <!-- Main content -->
    <main class="main-content">
      <header class="header">
        <h1>Reservation 學生機位預約表</h1>
        <div class="search-bar">
          <el-input v-model="searchTerm" placeholder="請輸入要查詢的學號" class="search-input" clearable />
        </div>
      </header>

      <!-- Table -->
      <div class="table-wrapper">
        <el-table :data="displayedReservations" border class="table">
          <el-table-column prop="reservation_id" label="預約編號" align="center"/>
          <el-table-column prop="student_id" label="學生編號" align="center"/>
          <el-table-column prop="seat_id" label="座位編號" align="center"/>
          <el-table-column prop="timeslot_id" label="時段編號" align="center"/>
          <el-table-column prop="create_time" label="創建時間" align="center">
          <template #default="{ row }">
            {{ new Date(row.create_time).toISOString().slice(0, 19).replace("T", " ") }}
          </template>
          </el-table-column>
        </el-table>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from "vue";
import type { Reservation } from "./interfaces/Reservations";
import { asyncGet } from "./utils/fetch";
import { apis } from "./enum/api";

const reservations = ref<Array<Reservation>>([]);
const searchTerm = ref("");

onMounted(() => {
  asyncGet(apis.test).then((resp: Array<Reservation>) => {
    reservations.value = resp;
  });
});

const displayedReservations = computed(() => {

  if (!searchTerm.value) {
    return reservations.value;
  }

  const filtered = [];
  for (const item of reservations.value) {
    if (item.student_id.includes(searchTerm.value)) {
      filtered.push(item);
    }
  }
  return filtered;
});

</script>

<style scoped lang="scss">

.main-container {
  display: flex;
  font-size: 20px;
  background: #ece9e6;
}

.app-title {
  font-size: 22px;
  font-weight: bold;
  margin-bottom: 25px;
  text-align: center;
}

.main-content {
  flex: 1;
  padding: 40px;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 25px;
}

.table-wrapper {
  border-radius: 12px;
  background: #f9f9f9;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.12);
  padding: 20px;
  border: none;
  overflow: hidden;
}

.search-bar {
  display: flex;
  gap: 15px;
}

.search-input {
  width: 275px;
  margin-left: 0;
  border-radius: 8px;
}

:deep(.el-table__header-wrapper th) {
  background-color: #ffffff;
  color: #444;
  font-weight: 600;
  border-bottom: 1px solid #ddd;
  text-transform: none;
  padding: 15px 10px;
  text-align: center;
}

:deep(.el-table__row:hover) {
  background: rgba(66, 133, 244, 0.08);
  transition: background 0.3s ease;
}

::-webkit-scrollbar {
  width: 10px;
  border-radius: 10px;
}

::-webkit-scrollbar-thumb {
  background: #bbb;
  border-radius: 10px;
}

::-webkit-scrollbar-track {
  background: #e8e8e8;
  border-radius: 10px;
}

:deep(.el-table__body tr) {
  transition: background-color 0.3s ease;
}
</style>