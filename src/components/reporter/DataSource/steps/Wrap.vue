<template>
  <b-card
    class="shadow-sm"
    header-bg-variant="light"
    footer-bg-variant="white"
  >
    <template #header>
      <h5 class="mb-0">
        <slot name="header-name" />

        <b-button
          variant="link"
          @click="collapsed = !collapsed"
        >
          // collapse
        </b-button>
      </h5>
    </template>

    <fieldset @click.prevent.stop v-show="!collapsed">
      <slot name="body-fields" />
    </fieldset>
  </b-card>
</template>

<script>
export default {
  name: 'SWrap',

  data () {
    return {
      collapsed: false,
    }
  },
}
</script>
