<template>
  <div id="app">
    <b-container class="h-100 d-flex">
      <b-row
        align-v="center"
        class="h-75 align-self-center w-100 contentContainer"
      >
        <b-col class="justify-content-center">
          <todo-renderer
            v-for="todo in todos"
            :key="todo.id"
            :todo="todo"
            @discardEdition="discardEdition"
            @saveEditedTodo="saveTodo"
          />
          <todo-renderer
            :todo="newTodo"
            force-edition
            v-if="addNewTodo"
            @discardEdition="cancelAddingNewTodo"
          />
          <div class="container-fluid" v-if="!addNewTodo">
            <b-row class="align-self-center w-100">
              <b-col class="d-flex justify-content-center">
                <b-btn pill variant="success" @click="activateAddNewTodo">
                  <b-icon-plus-circle-fill></b-icon-plus-circle-fill>
                </b-btn>
              </b-col>
            </b-row>
          </div>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import todoRenderer from "./components/todoRenderer.vue";
import TodoRenderer from "./components/todoRenderer.vue";

export default {
  name: "App",
  components: {
    todoRenderer,
    TodoRenderer,
  },
  data() {
    return {
      todos: {
        1: { id: 1, owner: "Mark", text: "Kupić ogóry", done: false },
        2: { id: 2, owner: "Mark", text: "Kupić papier", done: false },
      },
      newTodo: { owner: "", text: "" },
      addNewTodo: false,
    };
  },
  methods: {
    activateAddNewTodo() {
      this.newTodo = { owner: "", text: "" };
      this.addNewTodo = true;
    },
    discardEdition(params) {
      this.todos[params.todo.id].owner = params.rememberedOwner;
      this.todos[params.todo.id].text = params.rememberedText;
    },
    saveTodo() {},
    cancelAddingNewTodo() {
      this.addNewTodo = false;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-image: url("./assets/background.jpg");
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  background-color: chocolate;
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
}

.contentContainer {
  background-color: rgba(255, 255, 255, 0.55);
  border: 10px solid rgba(255, 255, 255, 0.6);
  border-radius: 20px;
}
</style>
