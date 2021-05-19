<template>
  <component
    :is="cmp"
    v-on="$listeners"
    v-bind="$props"
  />
</template>

<script>
import base from '../base.vue'
import CTGeneric from './Generic/index.vue'
import CTStacked from './Stacked/index.vue'

export default {
  extends: base,

  computed: {
    cmp () {
      if (!this.projection) {
        return undefined
      }

      switch (this.projection.variant) {
        case 'stacked':
          return CTStacked

        case 'regular':
          return CTGeneric
        case '':
          return CTGeneric
      }
      throw new Error(`unknown table element variant: ${this.projection.variant}`)
    }
  }
}
</script>
