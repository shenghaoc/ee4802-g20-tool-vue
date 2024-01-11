<template>
  <t-head-menu>
    <template #logo>
      <h1>Price Prediction</h1>
    </template>
  </t-head-menu>
  <t-form :model="form" labt-width="120px">
    <t-form-item label="ML Model">
      <client-only>
        <t-select v-model="form.ml_model"
        :options="ml_model_list_lv"
        placeholder="Please select"
        size="large"
        />
      </client-only>
    </t-form-item>
    <t-form-item label="Town">
      <client-only>
        <t-select v-model="form.town"
        :options="town_list_lv"
        placeholder="Please select"
        size="large"
        />
      </client-only>
    </t-form-item>
    <t-form-item label="Storey Range">
      <client-only>
        <t-select v-model="form.storey_range"
        :options="storey_range_list_lv"
        placeholder="Please select"
        size="large"
        />
      </client-only>
    </t-form-item>
    <t-form-item label="Flat Model">
      <client-only>
        <t-select v-model="form.flat_model"
        :options="flat_model_list_lv"
        placeholder="Please select"
        size="large"
        />
      </client-only>
    </t-form-item>
    <t-form-item label="Floor Area">
      <t-input-number v-model="form.floor_area_sqm"
      :min="1"
      />
    </t-form-item>
    <t-form-item label="Lease Commence Date">
      <client-only>
        <t-date-picker v-model="form.lease_commence_date"
        mode="year"
        placeholder="Pick a year"
        :disable-date="{
          before: dayjs.utc('1960-01', 'YYYY-MM').format(),
          after: dayjs.utc('2022-02', 'YYYY-MM').format()
        }"
        />
      </client-only>
    </t-form-item>
    <t-form-item>
      <t-button theme="primary" @click="onSubmit">Submit</t-button>
    </t-form-item>
  </t-form>
  <t-statistic title="Prediction" :value=output prefix="$" />
  <LineChart :chartData="chartData" :key="output" />
</template>

<script lang="ts" setup>
import { ref, reactive } from 'vue'

import {
  Button as TButton,
  DatePicker as TDatePicker,
  Select as TSelect,
  InputNumber as TInputNumber,
  Form as TForm,
  FormItem as TFormItem,
  HeadMenu as THeadMenu,
  Statistic as TStatistic
} from "tdesign-vue-next";

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
