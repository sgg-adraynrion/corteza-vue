<template>
  <div>
    <h5>
      {{ projection.name }}
    </h5>
    <div class="w-100 h-100">
      <canvas
        ref="chart"
        class="mh-100 w-auto m-auto"
      />
    </div>
  </div>
</template>

<script>
import Chart from 'chart.js'
import base from '../base.vue'

export default {
  extends: base,

  data () {
    return {
      chart: undefined,
    }
  },

  watch: {
    dataset: {
      handler (ds) {
        this.$nextTick(() => {
          this.renderChart(ds)
        })
      },
      deep: true,
      immediate: true,
    },
  },

  methods: {
    renderChart (dataset) {
      if (this.chart) {
        if (this.chart) {
          this.chart.destroy()
        }
      }

      const ctx = this.$refs.chart.getContext('2d')
      this.chart = new Chart(ctx, this.makeChartConfig(this.projection, this.dataset))
    },

    makeChartConfig (chart, ds) {
      return {
        type: chart.options[0].chartType,
        data: {
          labels: this.getLabels(chart, ds),
          datasets: this.getChartDatasets(chart, ds),
        },
        // options: {
        //   scales: {
        //     yAxes: [{
        //       ticks: {
        //         beginAtZero: true,
        //       },
        //     }],
        //   },
        // },
      }
    },

    getLabels (chart, ds) {
      const labels = []
      const lx = this.getColIndex(ds, chart.options[0].labelColumn)
      if (lx < 0) {
        throw new Error(`Column ${chart.options[0].labelColumn} not found; @todo i18n`)
      }

      for (const row of ds.rows) {
        labels.push(row[lx].variable)
      }

      return labels
    },

    getChartDatasets (chart, ds) {
      const rr = []

      // Find indexes for all columns we wish to have
      const dsx = []
      for (const dp of chart.options[0].dataColumns) {
        const i = this.getColIndex(ds, dp.name)
        if (i < 0) {
          throw new Error(`Column ${dp.name} not found; @todo i18n`)
        }
        dsx.push(i)
      }

      // Now make all of the datasets
      for (const i of dsx) {
        const d = {
          label: ds.columns[i].name,
          backgroundColor: [
            '#48639C',
            '#4C4C9D',
            '#712F79',
            '#976391',
            '#F7996E',
            '#DC0073',
          ],
          data: [],
        }
        rr.push(d)

        for (const row of ds.rows) {
          d.data.push(row[i].variable)
        }
      }

      return rr
    },

    getColIndex (ds, col) {
      if (!ds || !ds.columns) return -1

      return ds.columns.findIndex(({ name }) => name === col)
    },
  },
}
</script>
