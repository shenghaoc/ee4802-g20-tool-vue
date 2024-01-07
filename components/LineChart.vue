<template>
  <Line :data="chartData" :options="chartOptions" />
</template>

<script lang="ts">
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title as chartTitle,
  Tooltip,
  Legend
} from 'chart.js'
import { Line } from 'vue-chartjs'

ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  chartTitle,
  Tooltip,
  Legend
);

import dayjs, { Dayjs } from 'dayjs';
import customParseFormat from 'dayjs/plugin/customParseFormat';
import utc from 'dayjs/plugin/utc';

dayjs.extend(customParseFormat);
dayjs.extend(utc);

let curr = dayjs.utc('2022-02', 'YYYY-MM');
let labels = [...Array(13).keys()]
  .reverse()
  .map((x) => curr.subtract(x, 'month').format('YYYY-MM'));

export default {
  name: 'LineChart',
  components: {
    Line
  },
  props: {
    chartData: {
      type: Object,
      required: true
    },
    chartOptions: {
      type: Object,
      default: {
        responsive: true,
        plugins: {
          legend: {
            position: 'top' as const
          },
          title: {
            display: false,
            text: 'Predicted Trends for Past 12 Months'
          }
        }
      }
    }
  }
}
</script>