<template>
  <div>
    <h5>
      {{ projection.name }}
    </h5>
    <b-table-simple
      hover
      small
      caption-top
      responsive
    >
      <b-thead head-variant="light">
        <b-tr
          v-for="(r, i) in tabelify.columns"
          :key="i"
        >
          <b-th
            v-for="(c, j) in r"
            :key="j"
            v-bind="c.attrs"
          >
            {{ c.name }}
          </b-th>
        </b-tr>
      </b-thead>
      <b-tbody>
        <b-tr
          v-for="(r, i) in tabelify.rows"
          :key="i"
        >
          <b-td
            v-for="(c, k) in r"
            :key="k"
            v-bind="c.attrs"
          >
            <template v-if="c">
              {{ c.variable }}
            </template>
          </b-td>
        </b-tr>
      </b-tbody>
    </b-table-simple>
  </div>
</template>

<script>
import base from '../../base.vue'

export default {
  extends: base,

  computed: {
    tabelify () {
      if (!this.dataset) {
        return { rows: [], columns: [] }
      }

      return {
        columns: this.castDatasetColumns(this.dataset.columns, undefined, this.dataset.columns.length),
        rows: this.castDatasetRows(this.dataset),
      }
    },
  },

  methods: {
    castDatasetRows (ds) {
      const rows = []

      for (const r of ds.rows) {
        const row = []
        const attrs = {
          rowspan: 1,
        }

        rows.push(row)

        for (const c of r) {
          if (!c) {
            row.push({
              variable: undefined,
              attrs,
            })
          } else {
            if (c.matrix) {
              const aux = this.castDatasetRows(c.matrix)
              attrs.rowspan += (aux.length - 1)
              for (const ac of aux[0]) {
                if (ac) {
                  row.push(ac)
                }
              }
              rows.push(...aux.slice(1))
            } else {
              row.push({
                variable: c.variable,
                attrs,
              })
            }
          }
        }
      }

      return rows
    },

    castDatasetColumns (columns, padLeft = undefined, reqSize = undefined) {
      const row = []
      const rows = [row]
      const seenDimensions = new Set([])

      const rootDim = columns[0].dimension
      const attrs = { rowspan: 1 }

      for (const [i, c] of columns.entries()) {
        if (seenDimensions.has(c.dimension)) {
          continue
        }
        if (c.dimension && c.dimension !== rootDim) {
          const aux = this.castDatasetColumns(columns.slice(i), i, reqSize)

          row.push({
            name: c.dimension,
            attrs: { colspan: aux[0].length },
          })
          rows.push(...aux)

          attrs.rowspan += (aux[0].rowspan || 1)

          seenDimensions.add(c.dimension)
        } else if (rootDim && !c.dimension) {
          return rows
        } else {
          row.push({
            name: c.name,
            attrs,
          })
        }
      }

      return rows
    },
  },
}
</script>
