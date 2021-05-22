<template>
  <div class="home">
    <v-text-field
      v-model="newTaskTitle"
      class="pa-3"
      outlined
      label="Add task"
      append-icon="mdi-plus"
      hide-details
      clearable
      @click:append="addTask"
      @keyup.enter="addTask"
    >
    </v-text-field>

    <div v-if="tasks.length">
      <v-list class="pt-0" flat>
        <div v-for="task in tasks" :key="task.id">
          <v-list-item
            @click="doneTask(task.id)"
            :class="{ 'blue lighten-5': task.done }"
          >
            <template #default>
              <v-list-item-action>
                <v-checkbox :input-value="task.done"></v-checkbox>
              </v-list-item-action>

              <v-list-item-content>
                <v-list-item-title
                  :class="{ 'text-decoration-line-through': task.done }"
                  >{{ task.title }}</v-list-item-title
                >
              </v-list-item-content>

              <v-list-item-action>
                <v-btn icon @click.stop="deleteTask(task.id)">
                  <v-icon color="primary lighten-1">mdi-delete</v-icon>
                </v-btn>
              </v-list-item-action>
            </template>
          </v-list-item>

          <v-divider></v-divider>
        </div>
      </v-list>

      <v-snackbar v-model="snackbar">
        {{snackbarMessage}}

        <template v-slot:action="{ attrs }">
          <v-btn color="pink" text v-bind="attrs" @click="snackbar = false">
            Close
          </v-btn>
        </template>
      </v-snackbar>
    </div>

    <div v-else>
      <v-container>
        <v-row>
          <v-col class="d-flex justify-center align-center">
            <div class="mt-16">
              <v-icon color="blue lighten-4" class="text-h1">
                mdi-check
              </v-icon>

              <h2 class="blue--text text--lighten-4">No tasks</h2>
            </div>
          </v-col>
        </v-row>
      </v-container>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      snackbar: false,
      snackbarMessage: '',
      newTaskTitle: '',
      tasks: [
        {
          id: 1,
          title: 'Wake up',
          done: false,
        },
        {
          id: 2,
          title: 'Take bananas',
          done: false,
        },
        {
          id: 3,
          title: 'Eat bananas',
          done: false,
        },
      ],
    }
  },
  methods: {
    doneTask(id) {
      let task = this.tasks.filter((item) => item.id === id)[0]
      task.done = !task.done

      this.snackbarMessage = 'Task changed!'
      this.snackbar = true
    },
    deleteTask(id) {
      this.tasks = this.tasks.filter((item) => item.id !== id)

      this.snackbarMessage = 'Task removed!'
      this.snackbar = true
    },
    addTask() {
      this.tasks.push({ id: Date.now(), title: this.newTaskTitle, done: false })

      this.snackbarMessage = 'Task added!'
      this.snackbar = true
      this.newTaskTitle = ''
    },
  },
}
</script>
