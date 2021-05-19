<template>
  <step-wrap v-on="$listeners">
    <template #header-name>
      // Load // {{ step.load.name }}
    </template>

    <template #body-fields>
      <b-form-group
        label="// name">
        <b-form-input
          v-model="step.load.name"
          placeholder="name" />
      </b-form-group>

      <b-form-group
        label="// source">
        <b-form-input
          v-model="step.load.source"
          placeholder="source" />
      </b-form-group>

      <b-form-group
        label="// filter">
        <b-form-textarea
          v-model="step.load.filter"
          placeholder="filter" />
      </b-form-group>
    </template>
  </step-wrap>
</template>

<script>
import StepWrap from './Wrap.vue'
import base from './base.vue'

export default {
  name: 'SLoad',
  components: {
    StepWrap,
  },
  extends: base,
}
</script>
