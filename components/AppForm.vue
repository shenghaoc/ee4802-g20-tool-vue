<template>
  <el-form :model="form" label-width="120px">
    <el-form-item label="ML Model">
      <el-select-v2 v-model="form.ml_model"
      :options="ml_model_list_lv"
      placeholder="Please select"
      size="large"
      />
    </el-form-item>
    <el-form-item label="Town">
      <el-select-v2 v-model="form.town"
      :options="town_list_lv"
      placeholder="Please select"
      size="large"
      />
    </el-form-item>
    <el-form-item label="Storey Range">
      <el-select-v2 v-model="form.storey_range"
      :options="storey_range_list_lv"
      placeholder="Please select"
      size="large"
      />
    </el-form-item>
    <el-form-item label="Flat Model">
      <el-select-v2 v-model="form.flat_model"
      :options="flat_model_list_lv"
      placeholder="Please select"
      size="large"
      />
    </el-form-item>
    <el-form-item label="Floor Area">
      <el-input-number v-model="form.floor_area_sqm"
      :min="1"
      />
    </el-form-item>
    <el-form-item label="Lease Commence Date">
      <el-date-picker v-model="form.lease_commence_date"
      type="year"
      placeholder="Pick a year"
      />
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="onSubmit">Submit</el-button>
    </el-form-item>
  </el-form>
  <LineChart :chartData="chartData" :key="output" />
</template>

<script lang="ts" setup>
import { ref, reactive } from 'vue'

import {
  ElSelectV2,
  ElInputNumber,
  ElDatePicker,
  ElForm,
  ElFormItem,
} from "element-plus";

import { ml_model_list } from '~/assets/lists';
import { town_list } from '~/assets/lists';
import { storey_range_list } from '~/assets/lists';
import { flat_model_list } from '~/assets/lists';

const ml_model_list_lv = ml_model_list.map((ml_model, _) => ({ "label": ml_model, "value": ml_model }));
const town_list_lv = town_list.map((town, _) => ({ "label": town, "value": town }));
const storey_range_list_lv = storey_range_list.map((storey_range, _) => ({ "label": storey_range, "value": storey_range }));
const flat_model_list_lv = flat_model_list.map((flat_model, _) => ({ "label": flat_model, "value": flat_model }));

import dayjs, { Dayjs } from 'dayjs';
import customParseFormat from 'dayjs/plugin/customParseFormat';
import utc from 'dayjs/plugin/utc';

dayjs.extend(customParseFormat);
dayjs.extend(utc);

let curr = dayjs.utc('2022-02', 'YYYY-MM');
let labels = [...Array(13).keys()]
  .reverse()
  .map((x) => curr.subtract(x, 'month').format('YYYY-MM'));


const output = ref(0.0);
let chartData = {
  labels: labels,
  datasets: [
    {
      label: 'Sample Trends',
      data: labels.map(() => 0.0),
      borderColor: 'rgb(255, 99, 132)',
      backgroundColor: 'rgba(255, 99, 132, 0.5)'
    }
  ]
}

// do not use same name with ref
const form = reactive({
  ml_model: 'Support Vector Regression',
  town: 'ANG MO KIO',
  storey_range: '01 TO 03',
  flat_model: '2-room',
  floor_area_sqm: 1,
  lease_commence_date: curr.toDate()
})

const onSubmit = () => {
  const res = fetch(
    'https://ee4802-g20-tool.schoenherrchen.workers.dev/api/prices?' +
    new URLSearchParams({
      ml_model: form.ml_model,
      month_start: curr.subtract(12, 'month').format('YYYY-MM'),
      month_end: curr.format('YYYY-MM'),
      town: form.town,
      storey_range: form.storey_range,
      flat_model: form.flat_model,
      floor_area_sqm: form.floor_area_sqm.toString(),
      lease_commence_date: dayjs(form.lease_commence_date).year().toString()
    }),
    {
      method: 'GET' // *GET, POST, PUT, DELETE, etc.
    }
  );
  res.then((response) =>
    response.json().then((server_data: [{ labels: string; data: number }]) => {
      chartData = {
        labels: server_data.map((x: { labels: string; data: number }) => x['labels']),
        datasets: [
          {
            label: 'Trends',
            data: server_data.map((x: { labels: string; data: number }) => x['data']),
            borderColor: 'rgb(53, 162, 235)',
            backgroundColor: 'rgba(53, 162, 235, 0.5)'
          }
        ]
      };
      output.value = server_data[server_data.length - 1]['data'];
    })
  );
}
</script>
