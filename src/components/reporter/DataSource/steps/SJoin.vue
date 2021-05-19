<template>
  <step-wrap v-on="$listeners">
    <template #header-name>
      // Join // {{ step.join.name }}
    </template>

    <template #body-fields>
      <b-form-group
        label="// name">
        <b-form-input
          v-model="step.join.name"
          placeholder="name" />
      </b-form-group>

      <b-form-group
        label="// local dimension">
        <b-form-input
          v-model="step.join.local"
          placeholder="dimension.field" />
      </b-form-group>

      <b-form-group
        label="// foreign dimension">
        <b-form-input
          v-model="step.join.foreign"
          placeholder="dimension.field" />
      </b-form-group>
    </template>
  </step-wrap>
</template>

<script>
import StepWrap from './Wrap.vue'
import base from './base.vue'

export default {
  name: 'SJoin',
  components: {
    StepWrap,
  },
  extends: base,
}
</script>
