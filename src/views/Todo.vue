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
                <div class="d-flex justify-end">
                  
                  <v-list-item-action-text v-if="task.due">
                    <v-icon>mdi-calendar</v-icon>
                    {{ task.due }}
                  </v-list-item-action-text>

                  <v-menu bottom left>
                    <template v-slot:activator="{ on }">
                      <v-btn dark icon v-on="on">
                        <v-icon color="primary">mdi-dots-vertical</v-icon>
                      </v-btn>
                    </template>

                    <v-list>
                      <v-list-item
                        v-for="(item, i) in items"
                        :key="i"
                        @click="taskAction(item.title, task.id)"
                        link
                      >
                        <v-list-item-action>
                          <v-icon>{{ item.icon }}</v-icon>
                        </v-list-item-action>

                        <v-list-item-content>
                          <v-list-item-title>{{
                            item.title
                          }}</v-list-item-title>
                        </v-list-item-content>
                      </v-list-item>
                    </v-list>
                  </v-menu>
                </div>
              </v-list-item-action>
            </template>
          </v-list-item>
          <v-divider></v-divider>
        </div>
      </v-list>

      <v-snackbar v-model="snackbar">
        {{ snackbarMessage }}

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

    <v-dialog v-model="editDialog">
      <v-card>
        <v-card-title> Edit task </v-card-title>

        <v-card-text>
          Edit the title of the task:

          <v-container>
            <v-row>
              <v-col>
                <v-text-field
                  label="Task"
                  v-model="currentTask.title"
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn plain @click="editDialog = false"> CANCEL </v-btn>
          <v-btn plain color="red" @click="saveEdit"> SAVE </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="showCalendar">
      <v-card>
        <v-card-text>
          <v-row justify="center">
            <v-date-picker class="mt-4" v-model="datePicker"></v-date-picker>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn plain @click="showCalendar = false"> CANCEL </v-btn>
          <v-btn plain color="red" @click="saveDate()"> OK </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>


    <v-dialog v-model="confirmDeleteDialog">
      <v-card>
        <v-card-title>Delete task?</v-card-title>

        <v-card-text>
          Are you sure you wanna delete this task?
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn plain @click="confirmDeleteDialog = false"> NO </v-btn>
          <v-btn plain color="red" @click="confirmDelete"> YES </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      confirmDeleteDialog: false,
      showCalendar: false,
      datePicker: null,
      currentTask: {},
      editDialog: false,
      items: [
        { title: 'Edit', icon: 'mdi-pencil' },
        { title: 'Due Date', icon: 'mdi-calendar' },
        { title: 'Delete', icon: 'mdi-delete' },
        { title: 'Sort', icon: 'mdi-sort' },
      ],
      snackbar: false,
      snackbarMessage: '',
      newTaskTitle: '',
      tasks: [
        {
          id: 1,
          title: 'Wake up',
          done: false,
          due: null,
        },
        {
          id: 2,
          title: 'Take bananas',
          done: false,
          due: null,
        },
        {
          id: 3,
          title: 'Eat bananas',
          done: false,
          due: null,
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
    saveEdit() {
      const taskFound = this.tasks.find(
        (item) => item.id === this.currentTask.id
      )
      taskFound.title = this.currentTask.title
      this.editDialog = false

      this.snackbarMessage = 'Task updated!'
      this.snackbar = true
    },
    taskAction(action, taskId) {
      
      const taskFound = this.tasks.find((item) => item.id === taskId)
      this.currentTask = taskFound

      if (action === 'Edit') {  
        this.editDialog = true
        return
      }

      if (action === 'Due Date') {
        this.showCalendar = true
        return
      }

      if (action === 'Delete') {
        this.confirmDeleteDialog = true
        return
      }
    },
    saveDate() {
      this.currentTask.due = this.datePicker
      this.showCalendar = false
    },
    confirmDelete() {
      this.deleteTask(this.currentTask.id)
      this.confirmDeleteDialog = false
    }
  },
}
</script>
