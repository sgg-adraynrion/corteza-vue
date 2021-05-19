<template>
  <b-row>
    <b-col cols="4">
      <b-form-group
        v-if="editing"
        label="//Name"
      >
        <b-form-input
          v-model="projection.name"
          type="text"
        />
      </b-form-group>
      <h4 v-else>
        {{ projection.name }}
      </h4>

      <b-form-group
        v-if="editing"
        label="//Description"
      >
        <b-form-textarea
          v-model="projection.description"
        />
      </b-form-group>
      <p
        v-else-if="projection.description"
      >
        <small>
          {{ projection.description }}
        </small>
      </p>
    </b-col>
    <b-col cols="8">
      <b-card
        class="shadow-sm projection"
        footer-bg-variant="white"
      >
        <div>
          <draggable
            v-model="projection.elements"
            tag="div"
            :options="{
              group: 'display',
            }"
          >
            <div
              v-for="(row, i) of projection.elements"
              :key="'r'+i"
              class="p-row d-inline-flex w-100"
            >
              <component
                :is="elementFor(e)"
                v-for="(e, j) of row"
                :key="'e'+j"
                class="p-col w-100 h-100"
                :class="['p-' + e.kind]"
                :projection="e"
                :dataset="datasetFor(projection, e)"
                :editing="editing"
              />
            </div>
          </draggable>
        </div>

        <template v-if="editing">
          <hr>
          <div class="mt-5">
            // Add display element
            <ul>
              <li
                v-for="(kind, i) in displayElements"
                :key="'k' + i"
              >
                // {{ kind.kind }}
                <ul>
                  <li
                    v-for="(variant, j) in kind.variants"
                    :key="'v' + j"
                  >
                    <b-btn
                      variant="link"
                      @click="addDisplayElement(kind, variant)"
                    >
                      // {{ variant.variant }}
                    </b-btn>
                  </li>
                </ul>
              </li>
            </ul>
          </div>
        </template>
      </b-card>
    </b-col>
  </b-row>
</template>

<script>
import * as loader from './elements/loader.ts'
import draggable from 'vuedraggable'
import { reporter } from '@cortezaproject/corteza-js'

export default {
  name: 'RProjection',

  components: {
    draggable,
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
    projection: {
      type: Object,
      required: true,
    },
  },

  methods: {
    elementFor (p) {
      switch (p.kind) {
        case 'table':
          return loader.PTable
        case 'chart':
          return loader.PChart
      }
    },

    datasetFor (p, e) {
      if (!this.dataset) {
        return undefined
      }

      const k = `${p.key}[${e.name}]`
      const dsx = this.dataset.columns.findIndex(({ name }) => name === k)
      if (dsx < 0) {
        throw new Error(`unable to find dataset for projection ${k}`)
      }
      return (this.dataset.rows[0][dsx] || {}).matrix
    },

    addDisplayElement (k, v) {
      if (!this.projection.elements) {
        this.projection.elements = []
      }
      const options = {}
      if (k.kind === 'chart') {
        options.type = v.variant.split('.').pop()
      }

      this.projection.elements.push([reporter.ElementFactory.Make({
        kind: k.kind,
        variant: k.variant,
        options,
      })])
    }
  },

  computed: {
    displayElements () {
      return [
        {
          kind: 'table',
          variants: [{ variant: 'generic' }, { variant: 'stacked' }],
        },

        {
          kind: 'chart',
          variants: [{ variant: 'generic.pie' }],
        },
      ]
    },
  },
}
</script>

<style lang="scss" scoped>
.projection {
  .p-row {
    .p-col {
      border-radius: 15px;
      flex-grow: 1;
    }
  }
}

</style>
