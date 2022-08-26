<template>
  <div class="container-fluid p-2">
    <div class="row">
      <div class="col-xs-11 col-sm-10 col-md-9 col-lg-10 col-xl-10">
        <div class="row">
          <div class="col-xs-12 col-sm-12 col-md-4 col-lg-4 col-xl-4 d-flex">
            <b-form-input
              v-model="todo.owner"
              type="text"
              :disabled="!editionActive"
              :placeholder="this.ownerPlaceholder"
            />
          </div>
          <div class="col-xs-12 col-sm-12 col-md-8 col-lg-8 col-xl-8">
            <b-form-input
              v-model="todo.text"
              type="text"
              :disabled="!editionActive"
              :placeholder="this.textPlaceholder"
            />
          </div>
        </div>
      </div>
      <div class="col-xs-1 col-sm-2 col-md-3 col-lg-2 col-xl-2">
        <b-btn
          pill
          variant="primary"
          v-if="!editionActive"
          @click="activateEdition"
        >
          <b-icon-pencil-fill></b-icon-pencil-fill>
        </b-btn>
        <b-btn
          pill
          variant="danger"
          class="mr-1"
          v-if="editionActive"
          @click="discardEdition"
        >
          <b-icon-x-square-fill></b-icon-x-square-fill>
        </b-btn>
        <b-btn pill variant="success" v-if="editionActive">
          <b-icon-check-square-fill></b-icon-check-square-fill>
        </b-btn>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "TodoRenderer",
  data() {
    return {
      editionActive: false,
      rememberedOwner: "",
      rememberedText: "",
    };
  },
  props: {
    todo: { type: Object, required: true },
    forceEdition: {type: Boolean, default: false},
    ownerPlaceholder: {type: String, default: "Kto"},
    textPlaceholder: {type: String, default: "Co zrobić"},
  },
  mounted() {
    if (this.forceEdition) this.activateEdition();
  },
  methods: {
    activateEdition() {
      this.rememberedOwner = this.todo.owner;
      this.rememberedText = this.todo.text;
      this.editionActive = true;
    },
    saveData() {
      this.editionActive = false;
      this.$emit('saveEditedTodo', {owner: this.todo.owner, text: this.todo.text})
    },
    discardEdition() {
      this.editionActive = false;
      this.$emit('discardEdition', {todo: this.todo, rememberedOwner: this.rememberedOwner, rememberedText: this.rememberedText})
    },
  },
};
</script>

<style>
</style>