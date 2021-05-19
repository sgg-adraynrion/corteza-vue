<script>
export default {
  props: {
    projection: {
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
}
</script>
