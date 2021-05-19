<template>
  <b-row>
    <b-col cols="12">
      <report-meta
        :report="report"
        :editing="editing"
      />
    </b-col>

    <b-col
      class="mt-5"
      cols="12"
    >
      <b-col
        v-if="editing"
        cols="12"
      >
        <h5>
          // <b-btn variant="link" @click="editingProjections=true">projections</b-btn>
          // <b-btn variant="link" @click="editingProjections=false">data sources</b-btn>
        </h5>
      </b-col>

      <template v-if="(editing && editingProjections) || !!dataset">
        <report-projection
          v-for="(p, i) in report.projections || []"
          :key="`p-${i}`"
          class="mb-2 mt-4"
          :class="{ 'mt-0': i === 0 }"
          :report="report"
          :dataset="dataset"
          :projection="p"
          :editing="editing"
        />

        <b-btn
          v-if="editing"
          variant="link"
          @click="addProjection"
        >
          // + Add projection
        </b-btn>
      </template>
      <template v-else-if="(editing && !editingProjections)">
        <report-data-source
          :report="report"
          :editing="editing"
        />
      </template>
    </b-col>
  </b-row>
</template>

<script>
import ReportMeta from './Meta.vue'
import ReportProjection from './Projection.vue'
import ReportDataSource from './DataSource/index.vue'
import { reporter } from '@cortezaproject/corteza-js'

export default {
  name: 'ReportEditor',

  components: {
    ReportMeta,
    ReportProjection,
    ReportDataSource,
  },

  props: {
    report: {
      type: Object,
      required: true,
    },
    dataset: {
      type: Object,
      required: false,
      default: undefined,
    },
    editing: {
      type: Boolean,
      required: false,
      default: false,
    },
  },

  data () {
    return {
      editingProjections: false,
    }
  },

  methods: {
    addProjection () {
      if (!this.report.projections) {
        this.report.projections = []
      }
      this.report.projections.push(new reporter.Projection())
    },
  },
}
</script>
