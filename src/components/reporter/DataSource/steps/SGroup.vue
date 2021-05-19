<template>
  <step-wrap v-on="$listeners">
    <template #header-name>
      // Group // {{ step.group.name }}
    </template>

    <template #body-fields>
      <b-form-group
        label="// name">
        <b-form-input
          v-model="step.group.name"
          placeholder="name" />
      </b-form-group>

      <b-form-group
        label="// dimension">
        <b-form-input
          v-model="step.group.dimension"
          placeholder="dimension" />
      </b-form-group>

      <b-form-group
        label="// groups">
        <b-form-textarea
          v-model="step.group.groups"
          placeholder="groups" />
      </b-form-group>

      <b-form-group
        label="// columns">
        <b-form-textarea
          v-model="step.group.columns"
          placeholder="columns" />
      </b-form-group>
    </template>
  </step-wrap>
</template>

<script>
import StepWrap from './Wrap.vue'
import base from './base.vue'

export default {
  name: 'SGroup',
  components: {
    StepWrap,
  },
  extends: base,
}
</script>
