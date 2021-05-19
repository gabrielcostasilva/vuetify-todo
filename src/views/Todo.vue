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
  </div>
</template>

<script>
export default {
  data() {
    return {
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
    },
    deleteTask(id) {
      this.tasks = this.tasks.filter((item) => item.id !== id)
    },
    addTask() {
      this.tasks.push({ id: Date.now(), title: this.newTaskTitle, done: false })

      this.newTaskTitle = ''
    },
  },
}
</script>
