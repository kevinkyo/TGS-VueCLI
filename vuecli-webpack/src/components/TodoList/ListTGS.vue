<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>

        <v-card>
            <v-card-title primary-title>
                <v-text-field
                v-model="search"
                append-icon="mdi-magnify"
                label="search"
                single-line
                hide-details></v-text-field>
                <v-spacer></v-spacer>
                <v-btn color="primary" dark @click="dialog = true">Todo Selesai</v-btn>
                <v-btn color="success" dark @click="dialog = true">Tambah</v-btn>
            </v-card-title>
            
            <v-data-table :headers="headers" :items="filterPriority" :search="search" pagination.sync="pagination">
                <template v-slot:[`item.actions`]="{ item }">
                    <v-icon small color="red" class="mr-2" @click="editItem(item)">
                    mdi-pencil
                    </v-icon>
                    <v-icon color="blue" small @click="deleteItem(item)">
                    mdi-delete
                    </v-icon>
                </template>
            </v-data-table>
        </v-card>

        <v-dialog
            v-model="dialog"
            persistent
            max-width="600px"
            transition="dialog-transition"
        >
            <v-card>
                <v-card-title>
                    <span class="headline">From Todo</span>
                </v-card-title>
                
                <v-card-text>
					<v-container>
						<v-text-field v-model="formTodo.task" label="Task" required></v-text-field>
						<v-select v-model="formTodo.priority" :items="['Penting', 'Biasa', 'Tidak penting']" label="Priority" required></v-select>
						<v-textarea v-model="formTodo.note" label="Note" required></v-textarea>
					</v-container>
				</v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">Cancel</v-btn>
                    <v-btn color="blue darken-1" text @click="save">Save</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogDeleted" persistent max-width="350px">
			<v-card>
				<v-card-title>
					<span class="headline font-weight-bold">Mau hapus?</span>
				</v-card-title>

				<v-card-actions>
					<v-spacer></v-spacer>
					<v-btn color="red darken-1" text @click="confirmDelete">Ya</v-btn>
				</v-card-actions>
			</v-card>
		</v-dialog>

		<v-dialog v-model="dialogFinished" persistent max-width="800px">
			<v-card>
				<v-card-title>
					<span class="headline font-weight-bold">Finished To do</span>
				</v-card-title>
				<v-card-text>
					<v-container>
						<v-data-table :headers="finished" :items="finishedTodos">
							<template v-slot:[`item.priority`]="{ item }">
								<v-chip outlined label :color="getColor(item.priority)" dark>{{ item.priority }}</v-chip>
							</template>
						</v-data-table>
					</v-container>
				</v-card-text>
			</v-card>
		</v-dialog>
    </v-main>
</template>
<script>
export default {
    name:"List",
    data(){
        return {
            search:null,
            dialog:false,
            tempTodo:null,
            selected: [],
            headers: [
                {
                    text:'Task',
                    align: 'start',
                    sortable: true,
                    value: 'task',
                },
                { text: "Priority", value:"priority"},
                { text: "Note", value:"note"},
                { text: "Actions", value:"actions"},
                { text: "", value: "checkbox" },
            ],
            finished: [
				{
					text: "Task",
					align: "start",
					sortable: true,
					value: "task",
				},
				{ text: "Priority", value: "priority" },
				{ text: "Note", value: "note" },
			],
            todos: [
                {
                    task: "bernafas",
                    priority: "Penting",
                    note: "hufftts",
                },
                {
                    task: "nongkrong",
                    priority: "Tidak Penting",
                    note: "bersama teman teman"
                },
            ],
            finishedTodos: [

            ],
            formTodo:  {
                task: null,
                priority: null,
                note: null,
            },
        };
    },
    methods: {
        save(){
            if(index > -1){ // alias ga ketemu -1
                this.todos.push(this.index, 1, this.formTodo);
            }else {
                this.todos.push(this.form);
                this.resetForm;
            this.index = -1;
            this.dialog = false;
            }
        },
        cancel(){
            this.resetForm();
            this.dialog = false;
            this.index = -1;
        },
        resetForm(){
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
                selected: false,
            };
        },
        editItem(item){
            this.index = this.todos.findIndex(item);
            this.formTodo = {
                task : item.task,
                priority : item.priority,
                selected : item.selected,
                note : item.note,
            }
            this.dialog = true;
        },
        deleteItem(item){
            let x = window.confirm("Apa yakin ingin menghapus?  ");
            if(x === true){
                let index = this.findIndexTodos(item);
                this.todos.splice(index,1);
            }
        },
        cancelDelete(){
            this.dialogDeleted = false;
            this.index = -1;
        },
        confirmDelete(){
            this.finishedTodos.push(this.todos[this.index]);
            this.dialogDeleted = false;
            this.index = -1;
        },
        confirmDeleteMultiple() {
            this.finishedTodos.push(this.todos.filter((todo) => todo.selected));
            this.dialogDeleted = false;
            this.index = -1;
        },
        findIndexTodos(item){
            return this.todos.findIndex(obj => obj.task === item.task);
        },
         getColor(priority) {
			if (priority === "Penting") return "red";
			else if (priority === "Biasa") return "blue";
			else return "green";
		},
        
    },
};
</script>