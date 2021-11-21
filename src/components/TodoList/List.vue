<template>
  <v-main class="list">
    <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
    <v-card>
      <v-card-title>
        <v-text-field v-model="search" append-icon="mdi-magnify" label="Search" single-line hide-details></v-text-field>
        <v-spacer></v-spacer>
        <v-btn color="success" dark @click="dialog = true">Tambah</v-btn>
      </v-card-title>
      <v-data-table :headers="headers" :items="todos" :search="search">
        <template v-slot:[`item.actions`]="{ item }">
          <v-icon small class="mr-2" color="red" @click="editItem(item)"> mdi-pencil </v-icon>
          <v-icon small color="green" @click="deleteItem(item)"> mdi-delete </v-icon>
        </template>
      </v-data-table>
    </v-card>
    <!-- v-dialog for delete -->
    <v-dialog v-model="dialogDelete" max-width="500px">
      <v-card>
        <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
          <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
          <v-spacer></v-spacer>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- v-dialog for create task -->
    <v-dialog v-model="dialog" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Form Todo</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-text-field v-model="formTodo.task" label="Task" required></v-text-field>
            <v-select v-model="formTodo.priority" :items="['Penting', 'Biasa', 'Tidak Penting']" label="Priority"></v-select>
            <v-textarea v-model="formTodo.note" label="Note" required></v-textarea>
          </v-container>
        </v-card-text>
        <v-card-action>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="cancel">Cancel</v-btn>
          <v-btn color="blue darken-1" text @click="save">Save</v-btn>
        </v-card-action>
      </v-card>
    </v-dialog>
  </v-main>
</template>

<script>
export default {
  name: "List",
  data() {
    return {
      search: null,
      dialog: false,
      dialogDelete: false,
      editOrcreate: 0,
      headers: [
        {
          text: "Task",
          align: "start",
          sortable: true,
          value: "task",
        },
        { text: "Priority", value: "priority" },
        { text: "Note", value: "note" },
        { text: "Actions", value: "actions" },
      ],
      todos: [
        {
          task: "Coding",
          priority: "Penting",
          note: "Code for your life",
        },
        {
          task: "Gaming",
          priority: "Tidak Penting",
          note: "Wasting time",
        },
        {
          task: "Masak",
          priority: "Biasa",
          note: "Indomie saaat coding terbaik gan",
        },
      ],
      formTodo: { task: null, priority: null, note: null },
    };
  },

  methods: {
        save() {
            if(this.editOrcreate == 0) {
                this.todos.push(this.formTodo);
                this.resetForm();
            }
            else if(this.editOrcreate == 1) {
                this.todos[this.index].task = this.formTodo.task;
                this.todos[this.index].priority = this.formTodo.priority;
                this.todos[this.index].note = this.formTodo.note;
                this.resetForm();
                this.editOrcreate = 0;
            }
            this.dialog = false;
        },
    cancel() {
      this.resetForm();
      this.dialog = false;
    },
    resetForm() {
      this.formTodo = { task: null, priority: null, note: null };
    },
    deleteItem(item) {
      this.editedIndex = this.todos.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },
    editItem(item){
      this.index = this.todos.indexOf(item);
      this.editOrcreate = 1;
      this.dialog = true;
      this.formTodo.task = item.task;
      this.formTodo.priority = item.priority;
      this.formTodo.note = item.note;
    },
    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    deleteItemConfirm() {
      this.todos.splice(this.editedIndex, 1);
      this.closeDelete();
    },
  },
};
</script>
