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
  </el-form>
</template>

<script lang="ts" setup>
import { reactive } from 'vue'

import {
  ElSelectV2,
  ElInputNumber,
  ElDatePicker,
  ElForm,
  ElFormItem,
} from "element-plus";

import url_map from '~/assets/url.json'
import town_list from '~/assets/town.json'
import storey_range_map from '~/assets/storey_range.json'
import flat_model_list from '~/assets/flat_model.json'

const ml_model_list_lv = Object.keys(url_map).map((ml_model, idx) => ({ "label": ml_model, "value": idx }));
const town_list_lv = town_list.map((town, idx) => ({ "label": town, "value": idx }));
const storey_range_list_lv = Object.keys(storey_range_map).map((storey_range, idx) => ({ "label": storey_range, "value": idx }));
const flat_model_list_lv = flat_model_list.map((flat_model, idx) => ({ "label": flat_model, "value": idx }));

// do not use same name with ref
const form = reactive({
  ml_model: '',
  town: '',
  storey_range: '',
  flat_model: '',
  floor_area_sqm: 1,
  lease_commence_date: new Date(2022, 1)
})

</script>
