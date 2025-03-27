<template>
  <div
    v-if="show"
    :class="['modal fade', { show: 'show' }]"
    id="exampleModal"
    tabindex="-1"
    role="dialog"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
    :style="{ display: show ? 'block' : 'none' }"
  >
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">Are you sure ?</h5>
        </div>
        <div class="modal-body">{{ message }}</div>
        <div class="modal-footer">
          <button type="button" @click="cancel" class="btn btn-secondary">
            No
          </button>
          <button type="button" @click="confirm" class="btn btn-primary">
            Yes
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ConfirmationModal",
  props: ["id", "message", "show"],
  emits: ["confirm", "cancel"],
  methods: {
    confirm() {
      this.$emit("confirm", this.$props.id);
    },
    cancel() {
      this.$emit("cancel");
    },
  },
  mounted() {},
};
</script>
