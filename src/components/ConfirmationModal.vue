<template>
  <div>
    <div v-if="show" class="modal-backdrop fade show"></div>

    <div
      v-if="show"
      class="modal fade show"
      id="exampleModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      :style="{ display: show ? 'block' : 'none' }"
    >
      <div class="modal-dialog modal-dialog-centered" role="document">
        <div class="modal-content">
          <div class="modal-header d-flex justify-content-center">
            <h3 class="modal-title" id="exampleModalLabel">Are you sure ?</h3>
          </div>
          <div class="modal-body text-center">{{ message }}</div>
          <div class="modal-footer d-flex justify-content-center">
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
