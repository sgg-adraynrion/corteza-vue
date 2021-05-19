<template>
  <div class="h-100">
    <component
      :is="getComp()"
      v-bind="$props"
      v-on="$listeners"
    />
  </div>
</template>

<script>
import base from '../base.vue'
import EConfigurator from './EConfigurator.vue'
import EViewer from './EViewer.vue'

export default {
  name: 'EChart',
  extends: base,
  methods: {
    getComp () {
      if (this.editing) {
        return EConfigurator
      }

      return EViewer
    },
  },
}
</script>
