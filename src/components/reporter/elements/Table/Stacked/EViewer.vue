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
      <b-tbody>
        <b-tr
          v-for="(r, i) in tabelify.rows"
          :key="i"
          :class="{
            'thead-light': isHeaderRow(r),
          }"
        >
          <template v-if="isHeaderRow(r)">
            <b-th
              v-for="(c, k) in r"
              :key="k"
              v-bind="c.attrs"
            >
              <template v-if="c">
                {{ c.name }}
              </template>
            </b-th>
          </template>
          <template v-else-if="isMetaRow(r)">
            <b-td
              v-bind="r[0].attrs"
              class="text-right"
            >
              &lt; 1 to 10 of x &gt;
            </b-td>
          </template>
          <template v-else>
            <b-td
              v-for="(c, k) in r"
              :key="k"
              v-bind="c.attrs"
            >
              <template v-if="c">
                {{ c.variable }}
              </template>
            </b-td>
          </template>
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

      const colIndex = this.castDatasetColumns(this.dataset.columns, undefined, this.dataset.columns.length)

      let rows = this.castDatasetRows(this.dataset, this.dataset.columns || [], colIndex || {})
      rows = this.assureRowWidth(rows)

      if (colIndex && colIndex['$root']) {
        rows.unshift(colIndex['$root'])

        for (const [i, c] of Object.entries(rows[1])) {
          rows[0][i].attrs.colspan = c.attrs.colspan
        }
      }

      // meta bits
      rows.push([{
        slot: 'paging',
        attrs: {
          colspan: rows[rows.length - 1][0].attrs.colspan,
        },
      }])

      return {
        columns: colIndex['$root'],
        rows,
      }
    },
  },

  methods: {
    castDatasetRows (ds, columns, colIndex) {
      const rows = []

      for (const r of ds.rows) {
        const row = []

        rows.push(row)

        for (const [i, c] of Object.entries(r)) {
          const col = columns[i]

          if (!c) {
            row.push({
              variable: undefined,
              attrs: {},
            })
          } else {
            if (c.matrix) {
              const aux = this.castDatasetRows(c.matrix, columns, colIndex)

              // header
              if (colIndex[col.dimension]) {
                rows.push(colIndex[col.dimension])
              }

              // body
              rows.push(...aux)

              // meta bits
              rows.push([{
                slot: 'paging',
                attrs: {
                  colspan: aux[aux.length - 1].length,
                },
              }])
            } else {
              row.push({
                variable: c.variable,
                attrs: {},
              })
            }
          }
        }
      }

      // meta bits
      // rows.push([{
      //   slot: 'paging'
      // }])
      return rows
    },

    assureRowWidth (rows) {
      let maxW = -1
      for (const r of rows) {
        maxW = Math.max(maxW, r.length)
      }

      if (maxW < 1) {
        return rows
      }

      // @todo when there are multiple columns, spread the delta across all of them.
      //       eg.: the row should define the span of 5; the first column takes 3, the second 2.
      //       make sure to cover cases with arbitrary columns.
      for (const r of rows) {
        if (r[0].attrs && !r[0].slot && maxW > r.length) {
          r[0].attrs.colspan = (maxW - r.length) + 1
        }
      }

      return rows
    },

    castDatasetColumns (columns, padLeft = undefined, reqSize = undefined) {
      // We will push the thead definition for each dataset dimension here
      const colIndex = {}

      for (const c of columns) {
        const dim = c.dimension || '$root'
        if (!colIndex[dim]) {
          colIndex[dim] = []
        }
        colIndex[dim].push({ name: c.name, attrs: {} })
      }

      return colIndex
    },

    isHeaderRow (row) {
      return row && !!row[0].name
    },

    isMetaRow (row) {
      return row && !!row[0].slot
    },
  },
}
</script>
