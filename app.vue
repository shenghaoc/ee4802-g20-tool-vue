<script setup>
import { ID_INJECTION_KEY } from 'element-plus'
provide(ID_INJECTION_KEY, {
  prefix: 100,
  current: 0,
})
</script>

<template>
  <h1>Price Prediction</h1>
<AppForm>
  This is an auto-imported component.
</AppForm>
</template>

