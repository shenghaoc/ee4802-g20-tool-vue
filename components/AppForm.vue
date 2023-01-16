<template>
  <el-form :model="form" label-width="120px">
    <el-form-item label="ML Model">
      <el-select-v2 v-model="form.ml_model" 
      :options="ml_model_list"
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

import url_map from '~/assets/url.json'

const ml_model_list = Object.entries(url_map).map(([label, value]) => ({label, value}))

// do not use same name with ref
const form = reactive({
  ml_model: ''
})

</script>
