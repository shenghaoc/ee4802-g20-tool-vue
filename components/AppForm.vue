<template>
  <el-form :model="form" label-width="120px">
    <el-form-item label="ML Model">
      <el-select-v2 v-model="form.ml_model" 
      :options="options"
      placeholder="Please select"
      size="large"
      />
    </el-form-item>

  </el-form>
</template>

<script lang="ts" setup>
import { reactive } from 'vue'

import {
  ElSelectV2,
  ElForm,
  ElFormItem,
} from "element-plus";

// do not use same name with ref
const form = reactive({
  ml_model: ''
})

const options = [
  {
    value: 'Support Vector Regression',
    label: 'Support Vector Regression',
  },
  {
    value: 'Ridge Regression',
    label: 'Ridge Regression',
  },
]
</script>
