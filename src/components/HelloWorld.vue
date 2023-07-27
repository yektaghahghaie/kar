<template>
  <h1>{{ msg }}</h1>
  <div :class="{ 'dark-mode': isDarkMode }">
    <div class="pdar">
      <button id="v" @click="toggleDarkMode">
        <img style="background-color: black" src="./icon-moon.svg" />
      </button>
    </div>
    <div class="bache"></div>
    <img src="./bg-desktop-light.jpg" />
    <h1>TODO</h1>
    <input
      type="text"
      style="width: 441px; margin-left: 40px; padding: 10px"
      v-model="newTaskText"
      @keyup.enter="add"
      :disabled="selectionMode"
      placeholder="O Currently typing"
    />
    <div class="koly">
      <ul v-if="tasks.length">
        <li class="kol" v-for="task in tasks" :key="task.id">
          <div>
            <input
              type="checkbox"
              v-model="task.checked"
              id="ve"
              v-bind:style="{ borderColor: checkbox }"
              @mouseover="change"
              @mouseout="reset"
            />
            <span
              :style="{
                textDecoration: task.checked ? 'line-through' : 'none',
              }"
              >{{ task.text }}</span
            >
          </div>
          <button @click="deletenote(task.id)">
            <img src="./icon-cross.svg" />
          </button>
        </li>
      </ul>
    </div>
    <div class="koly2">
      <div class="foter">
        <div class="node">{{ countSelectedTexts }} items left</div>
        <div class="item">
          <button @click="get" class="hover">All</button>
          <button @click="activateAll" class="hover1">Active</button>
          <button @click="deleteSelected" class="hover1">Completed</button>
        </div>
        <button class="hover1" @click="deleteChecked">Clear Completed</button>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "HelloWorld",
  data() {
    return {
      task: false,
      isDarkMode: false,
      tasks: [],
      newTaskText: "",
      selectionMode: false,
      checkbox: "black",
    };
  },

  computed: {
    itemCount() {
      return this.items.length;
    },
    countSelectedTexts() {
      return this.tasks.filter((task) => !task.checked).length;
    },
  },
  methods: {
    change() {
      document.getElementById("ve").style.cursor = "pointer";
      this.checkbox = "blue";
    },
    reset() {
      document.body.style.cursor = "default";
      this.checkbox = "black";
    },
    addTask() {
      if (this.newTaskText.trim() !== "") {
        this.tasks.push({
          text: this.newTaskText,
        });
        this.newTaskText = "";
      }
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    activateAll() {
      this.task = !this.task;
      for (let i = 0; i < this.tasks.length; i++) {
        this.tasks[i].active = !this.tasks[i].active;
      }
      this.tasks = this.tasks.filter((task) => !task.checked);
    },
    deleteSelected() {
      this.tasks = this.tasks.filter((task) => task.checked);
    },
    deleteChecked() {
      this.tasks = this.tasks.filter((task) => !task.checked);
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    toggleDarkMode() {
      this.isDarkMode = !this.isDarkMode;
      if (this.isDarkMode) {
        document.body.style.backgroundImage = "url('./icon-moon.svg')";
      } else {
        document.body.style.backgroundImage = "url('./icon-sun.svg')";
      }
    },
    get() {
      axios
        .get("http://localhost:3000/tasks")
        .then((response) => {
          this.tasks = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    add() {
      axios
        .post("http://localhost:3000/tasks", {
          text: this.newTaskText,
          completed: false,
        })
        .then((response) => {
          this.tasks.push(response.data);
          this.newTaskText = "";
        })
        .catch((error) => {
          console.log(error);
        });
    },
    deletenote(taskid) {
      axios
        .delete(`http://localhost:3000/tasks/${taskid}`)
        .then(() => {
          this.tasks = this.tasks.filter((task) => task.id !== taskid);
        })
        .catch((error) => {
          console.log(error);
        });
    },

    deleteAll() {
      axios
        .delete("http://localhost:3000/tasks")
        .then(() => {
          this.tasks = this.tasks.filter((task) => !task.checked);
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted() {
    if (localStorage.getItem("isDarkMode")) {
      this.isDarkMode = JSON.parse(localStorage.getItem("isDarkMode"));
    }
    if (this.isDarkMode) {
      document.body.style.backgroundImage = "url('./icon-moon.svg')";
    } else {
      document.body.style.backgroundImage = "url('./icon-sun.svg')";
    }
    const savedNotes = localStorage.getItem("tasks");
    if (savedNotes) {
      this.tasks = JSON.parse(savedNotes);
    }
    this.get();
  },
  watch: {
    isDarkMode(newVal) {
      localStorage.setItem("isDarkMode", newVal);
    },
    tasks: {
      deep: true,
      handler(newTaskText) {
        localStorage.setItem("tasks", JSON.stringify(newTaskText));
      },
    },
  },
};
</script>
<style scoped>
input[type="checkbox"] {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 2px solid #000;
}

input[type="checkbox"]:checked {
  background-color: blue;
}

input[type="checkbox"]:checked::before {
  content: "\2713";
  color: white;
}

.hover {
  color: gray;
}
.hover1 {
  color: gray;
}
.hover:hover {
  color: hsl(220, 98%, 61%);
}
.hover1:hover {
  color: hsl(210, 50%, 1%);
}
.rang {
  color: gray;
}
.text-selected {
  font-weight: bold;
}

.checked {
  text-decoration: line-through;
}

body {
  margin: auto;
  box-sizing: border-box;
  font-family: "x";
}
h1 {
  margin-right: 335px;
}
.pdar {
  position: relative;
  z-index: 0;
  top: 200px;
}
.bache {
  z-index: 1;
  top: 0px;
}
.dark-mode {
  background-color: #000;
  color: #fff;
}
button {
  border: none;
  background-color: #fff;
}
button:hover {
  font-weight: 20px;
  cursor: pointer;
}
li {
  list-style-type: none;
}
.foter {
  display: flex;
  border: 1px solid black;
  margin-left: 41px;
  padding: 10px;
  gap: 50px;
}
.node2 {
  width: 118px;
  color: gray;
}
.node {
  width: 118px;
  color: gray;
}
.node2:hover {
  background-color: #000;
  border: none;
}
.item {
  display: flex;
  gap: 4px;
  width: 110px;
  justify-content: center;
}
.kol {
  display: flex;
  border: 1px solid black;
  width: 441px;
  text-align: left;
  justify-content: space-between;
  padding: 10px;
}
.koly {
  display: flex;
  justify-content: center;
}
.koly2 {
  display: flex;
  justify-content: center;
  margin-top: -16px;
}
@media only screen and (max-width: 520px) {
}
</style>
