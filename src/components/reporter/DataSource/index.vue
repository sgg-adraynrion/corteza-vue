<template>
  <b-row>
    <b-col cols="2">
      <draggable
        tag="ul"
        v-model="stepIndex"
        :clone="makeStep"
        :options="{
          group: {
            name: 'steps',
            pull: 'clone',
            put: false,
          },
          sort: false,
        }"
      >
        <li
          v-for="{ name } in stepIndex"
          :key="name"
        >
            {{ name }}
        </li>
      </draggable>
    </b-col>

    <b-col cols="10">
      <div
        v-for="(source, sx) in report.sources"
        :key="`${source.name || ''}${sx}`"
        class="step-group-container"
      >
        <b-table-simple
          hover
          small
          caption-top
          responsive
        >
          <b-thead head-variant="light">
            <b-tr>
              <b-th
                v-for="(_, gx) in resolveSourceGroups(source)"
                :key="gx"
              >
                <b-form-group
                  v-if="editing"
                >
                  <b-form-input
                    v-model="source.groups[gx].name"
                    :placeholder="resolveGroupName(source.groups[gx])"
                  />
                </b-form-group>
                <h5 v-else>
                  {{ resolveGroupName(source.groups[gx]) }}
                </h5>
              </b-th>

              <b-th />
            </b-tr>
          </b-thead>

          <b-tbody>
            <b-tr>
              <b-td
                v-for="(group, k) in source.groups"
                :key="k"
              >
                <draggable
                  v-model="group.steps"
                  :options="{
                    group: 'steps',
                  }"
                >
                  <component
                    v-for="(step, i) in group.steps"
                    :key="i"
                    :is="getStepComponent(step)"
                    :step="step"
                    :class="{
                      'mt-3': i > 0,
                    }"
                  />
                </draggable>
              </b-td>
              <b-td>
                <b-btn
                  variant="link"
                  @click="addGroup(source)"
                >
                  // + Add
                </b-btn>
              </b-td>
            </b-tr>
          </b-tbody>
        </b-table-simple>
      </div>

      <b-btn
        variant="link"
        @click="addSource"
      >
        // + Add
      </b-btn>
    </b-col>
  </b-row>
</template>

<script>
import * as loader from './steps/loader.ts'
import draggable from 'vuedraggable'
import { reporter } from '@cortezaproject/corteza-js'

export default {
  components: {
    draggable,
  },

  props: {
    report: {
      type: Object,
      required: true,
    },
    editing: {
      type: Boolean,
      required: false,
      default: false,
    },
  },

  computed: {
    stepIndex: {
      get () {
        return Object.entries(loader)
          .sort(([a], [b]) => a < b)
          .map(([name, cmp]) => ({ name, cmp }))
      },
      set (sx) {},
    },
  },

  methods: {
    getStepComponent (s) {
      // @todo make better!
      if (s.load) {
        return loader.SLoad
      } else if (s.join) {
        return loader.SJoin
      } else if (s.group) {
        return loader.SGroup
      }
    },

    resolveGroupName (g) {
      if (g.name) {
        return g.name
      }

      // try to guess it
      // @todo look at the steps in the list; use the step type if they match

      // don't be too smart
      return '// Unnamed'
    },
  
    resolveSourceGroups (s) {
      return new Array(s.groups.length)
    },

    makeStep (s) {
      switch (s.name) {
        case 'SLoad':
          return reporter.StepFactory({ load: {} })
        case 'SJoin':
          return reporter.StepFactory({ join: {} })
        case 'SGroup':
          return reporter.StepFactory({ group: {} })
      }
    },

    addGroup (source) {
      source.groups.push({ name: '', steps: [] })
    },

    addSource () {
      this.report.sources.push({
        name: '',
        groups: [],
      })
    }
  },
}
</script>

<style lang="scss" scoped>
.step-group-container th {
  min-width: 300px;
}
</style>
