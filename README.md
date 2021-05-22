# Vuetify Todo
This project is based on ["Make Apps with Danny" Vuetify demonstration](https://www.youtube.com/watch?v=CjXgoYo86yY).

## Differences in this Branch

The original demonstration finishes with several challenges that are addressed in the author's course. I took it as a personal challenge and tried to implement some of them.

This branch features the second challenge: to add a notification when a task is added, deleted or changed.

<img src="./pics/Notification.gif" />

Firstly, I just copy and paste the `snackbar` component from Vuetify documentation. 

```
<v-snackbar v-model="snackbar">
  {{snackbarMessage}}

  <template v-slot:action="{ attrs }">
    <v-btn color="pink" text v-bind="attrs" @click="snackbar = false">
      Close
    </v-btn>
  </template>
</v-snackbar>
```

Then, I added the `snackbar` and `snackbarMessage` data properties into my `data()`. Whereas the former controls the snackbar visibility, the latter represents the message snackbar shows.

Finally, for each method, add, delete, change, I added two lines of code setting the message and the snackbar visibility.

```
this.snackbarMessage = 'Task added!'
this.snackbar = true 
```
