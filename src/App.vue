<template>
  <div className="container">
    <h1 className="header">TODO App</h1>
    <input
      placeholder="Add TODO"
      className="input"
      v-model="todoInput"
      @keyup="handleEnter"
    />
    <ul className="list">
      <li className="item" v-for="todo in todos.active" :key="todo.id">
        <span>{{ todo.value }}</span>
        <div className="item-buttons">
          <button className="remove-button" @click="removeTodo(todo.id, true)">
            Remove
          </button>
          <button className="done-button" @click="completeTodo(todo.id)">
            Done
          </button>
        </div>
      </li>
      <li v-if="todos.completed.length > 0" className="subheader">Completed</li>
      <li
        className="item completed"
        v-for="todo in todos.completed"
        :key="todo.id"
      >
        <span>{{ todo.value }}</span>
        <div className="item-buttons">
          <button className="restore-button" @click="restoreTodo(todo.id)">
            Restore
          </button>
          <button className="clear-button" @click="removeTodo(todo.id)">
            Clear
          </button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, ref } from "vue";

interface TODO {
  id: string;
  value: string;
}

interface TODOS {
  active: TODO[];
  completed: TODO[];
}

const randomId = (): string => {
  return `_${Math.random().toString(36).slice(2, 11)}`;
};

export default defineComponent({
  setup() {
    const todos = reactive<TODOS>({
      active: [
        { id: randomId(), value: "Say Hello World!" },
        { id: randomId(), value: "An uncompleted task" }
      ],
      completed: [{ id: randomId(), value: "Completed task" }]
    });

    const todoInput = ref("");

    const removeTodo = (id: string, isActive?: boolean) => {
      if (isActive) {
        todos.active.splice(
          todos.active.findIndex(todo => todo.id == id),
          1
        );
      } else {
        todos.completed.splice(
          todos.completed.findIndex(todo => todo.id === id),
          1
        );
      }
    };

    const restoreTodo = (id: string) => {
      const todo = todos.completed.find(todo => todo.id === id);
      if (todo) {
        todos.completed.splice(todos.completed.indexOf(todo), 1);
        todos.active.push(todo);
      }
    };

    const completeTodo = (id: string) => {
      const todo = todos.active.find(todo => todo.id === id);
      if (todo) {
        todos.active.splice(todos.active.indexOf(todo), 1);
        todos.completed.push(todo);
      }
    };

    const addTodo = () => {
      if (todoInput.value) {
        todos.active.push({ id: randomId(), value: todoInput.value });
        todoInput.value = "";
      }
    };

    const handleEnter = (event: KeyboardEvent) => {
      if (event.key === "Enter") {
        addTodo();
      }
    };
    return {
      todoInput,
      todos,
      removeTodo,
      restoreTodo,
      completeTodo,
      handleEnter
    };
  }
});
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: Arial, sans-serif;
  color: #374151;
}
.header {
  margin: 0;
}
.subheader {
  font-weight: bold;
  margin: 1rem 0;
}
.container {
  display: flex;
  justify-content: center;
  flex-direction: column;
  margin: 0.5rem;
  align-items: center;
  width: 30rem;
  max-width: 100%;
  background: #f3f4f6;
  border-radius: 1rem;
  padding: 1rem;
}
.input {
  background: #e5e7eb;
  box-sizing: border-box;
  width: 100%;
  border-radius: 1rem;
  font-size: 1.25rem;
  padding: 0.5rem;
  outline: none;
  border: none;
  margin: 0.5rem 0;
}
.list {
  font-size: 1.25rem;
  list-style: none;
  padding: 0;
  margin: 0;
  width: 100%;
}
.item {
  background: #e5e7eb;
  padding: 0.5rem;
  border-radius: 1rem;
  margin-top: 0.5rem;
}
.item.completed span {
  text-decoration: line-through;
}
.item-buttons {
  display: flex;
  justify-content: flex-end;
}
.item-buttons > button {
  transition: transform 150ms ease-out;
  cursor: pointer;
  padding: 0.5rem;
  border-radius: 1rem;
  margin-left: 0.5rem;
  color: #fff;
  outline: none;
  border: none;
}
.item-buttons > button:hover {
  transform: scale(1.05);
}
.done-button,
.clear-button {
  background: #10b981;
}
.remove-button,
.restore-button {
  background: #ef4444;
}
</style>
