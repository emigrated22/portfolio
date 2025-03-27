<script setup>
import { ref } from "vue";
import dataLab from '@/utils/api';
import { useChartStore } from "@/stores/chart.js";
const chartStore = useChartStore();
const startDate = ref("");
const endDate = ref("");
const timeUnit = ref("month");
const keywordGroups = ref([]);
const userInputGroupName = ref("");
const userInputKeyword = ref("");
const tempGroupName = ref("");
const tempKeywords = ref([]);
const toApiData = ref({});
function tempGroupAdd() {
 tempGroupName.value = userInputGroupName.value;
}
function tempKeywordAdd() {
 tempKeywords.value.push(userInputKeyword.value);
 userInputKeyword.value = "";
}
function makeGroup() {
 keywordGroups.value.push({
 groupName: tempGroupName.value,
 keywords: tempKeywords.value,
 });
 tempGroupName.value = "";
 tempKeywords.value = [];
 userInputGroupName.value = "";
}
async function sendResultToApi() {
 toApiData.value = {
 startDate: startDate.value,
 endDate: endDate.value,
 timeUnit: timeUnit.value,
 keywordGroups: keywordGroups.value,
 };
 await dataLab.post(toApiData.value);
 chartStore.makeChart();
}
</script>
<template>
 <div>시작일 <input type="date" v-model="startDate" /></div>
 <div>종료일 <input type="date" v-model="endDate" /></div>
 <select v-model="timeUnit">
 <option value="date">일간</option>
 <option value="week">주간</option>
 <option value="month">월간</option>
 </select>
 <div>
 그룹명: <input type="text" v-model="userInputGroupName" />
 <button v-on:click="tempGroupAdd">추가</button>
 {{ tempGroupName }}
 </div>
 <div>
 키워드: <input type="text" v-model="userInputKeyword" />
 <button v-on:click="tempKeywordAdd">추가</button>
 {{ tempKeywords }}
 </div>
 <div>
 <button v-on:click="makeGroup">그룹 확정</button>
 </div>
 <p>사용자 입력 그룹별 키워드</p>
 <ul v-if="keywordGroups.length">
    <li v-for="(keywordGroup, index) in keywordGroups" v-bind:key="index">
 <p>그룹 이름: {{ keywordGroup.groupName }}</p>
 <p>그룹 키워드: {{ keywordGroup.keywords }}</p>
 </li>
 </ul>
 <input type="submit" v-on:click="sendResultToApi" />
</template>