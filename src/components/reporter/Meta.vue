<template>
  <div>
    <template v-if="editing">
      <b-form-group
        label="//Name"
      >
        <b-form-input
          v-model="report.meta.name"
          type="text"
        />
      </b-form-group>

      <b-form-group
        label="//Handle"
      >
        <b-form-input
          v-model="report.handle"
          required
          type="text"
        />
      </b-form-group>

      <b-form-group
        label="//Dsc"
      >
        <b-form-textarea
          v-model="report.meta.description"
        />
      </b-form-group>
    </template>
    <template v-else>
      <!-- Make this configurable?  -->
      <!-- <h3>{{ report.meta.name }}</h3> -->
      <p>{{ report.meta.description }}</p>
    </template>
  </div>
</template>

<script>
export default {
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
}
</script>
