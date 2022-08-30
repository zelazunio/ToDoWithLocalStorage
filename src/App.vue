<template>
  <div id="app">
    <b-container class="h-100 d-flex">
      <b-row
        align-v="center"
        class="h-75 align-self-center w-100 contentContainer"
      >
        <b-col class="h-100 pt-2 pb-2 d-flex flex-column">
          <b-row>
            <b-col>
              <b-form-input
                v-model="nameSearch"
                placeholder="Name filter"
                class="m-2 w-50"
              />
            </b-col>
          </b-row>
          <b-row class="flex-grow-1" style="overflow-y: auto">
            <b-col>
              <todo-renderer
                v-for="todo in filteredTodos"
                :key="todo.id"
                :todo="todo"
                :names="owners"
                @discardEdition="discardEdition"
                @saveEditedTodo="saveEditedTodo"
                @deleteTodo="showDeleteTodoModal"
              />
            </b-col>
          </b-row>
          <b-row>
            <b-col>
              <todo-renderer
                :todo="newTodo"
                :names="owners"
                force-edition
                v-if="addNewTodo"
                @discardEdition="cancelAddingNewTodo"
                @saveEditedTodo="addTodo"
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
        </b-col>
      </b-row>
    </b-container>
    <b-modal v-model="showDeleteModal">
      <template v-slot:modal-header> Delete ToDo </template>
      Confirm operation
      <template v-slot:modal-footer>
        <b-btn variant="primary" @click="deleteTodo">OK</b-btn>
        <b-btn @click="showDeleteModal = false">Cancel</b-btn>
      </template>
    </b-modal>
  </div>
</template>

<script>
import todoRenderer from "./components/todoRenderer.vue";
import TodoRenderer from "./components/todoRenderer.vue";
import Vue from "vue";

export default {
  name: "App",
  components: {
    todoRenderer,
    TodoRenderer,
  },
  data() {
    return {
      todos: {},
      newTodo: { owner: "", text: "" },
      addNewTodo: false,
      showDeleteModal: false,
      todoToDeleteId: null,
      nameSearch: "",
    };
  },
  created() {
    try {
      this.todos = {
        ...this.todos,
        ...JSON.parse(localStorage.getItem("MyTodos")),
      };
    } catch (e) {
      console.debug(e);
    }
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
    saveEditedTodo(params) {
      let newTodo = { id: params.id, owner: params.owner, text: params.text };
      Vue.set(this.todos, params.id, newTodo);
      this.saveTodos();
    },
    addTodo(params) {
      let newTodoId = Math.max(...[0, ...Object.keys(this.todos)]) + 1;
      let newTodo = { id: newTodoId, owner: params.owner, text: params.text };
      Vue.set(this.todos, newTodoId, newTodo);
      this.saveTodos();
      this.addNewTodo = false;
    },
    saveTodos() {
      try {
        localStorage.setItem("MyTodos", JSON.stringify(this.todos));
      } catch (e) {
        console.debug(e);
      }
    },
    cancelAddingNewTodo() {
      this.addNewTodo = false;
    },
    showDeleteTodoModal(params) {
      this.todoToDeleteId = params.id;
      this.showDeleteModal = true;
    },
    deleteTodo() {
      Vue.delete(this.todos, this.todoToDeleteId);
      this.saveTodos();
      this.showDeleteModal = false;
    },
  },
  computed: {
    owners() {
      return Array.from(
        new Set(Object.values(this.todos).map((todo) => todo.owner))
      );
    },
    filteredTodos() {
      let result = {};
      Object.values(this.todos).forEach((todo) => {
        if (todo.owner.toLowerCase().includes(this.nameSearch.toLowerCase()))
          result[todo.id] = todo;
      });
      return result;
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
