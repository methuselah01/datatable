<template>
    <div id="app" data-app="true">
        <v-data-table
            v-model="selected"
            :search="search"
            :headers="headers"
            :items="todos"
            :items-per-page="10"
            :single-expand="singleExpand"
            :expanded.sync="expanded"
            item-key="title"
            show-select
            show-expand
            sort-by="title"
            class="elevation-1"
        >
            <template v-slot:top>
                <v-toolbar flat color="white">
                    <v-toolbar-title>Todo list</v-toolbar-title>
                    <v-divider
                        class="mx-4"
                        inset
                        vertical
                    ></v-divider>
                    <v-text-field
                        v-model="search"
                        append-icon="mdi-magnify"
                        label="Search"
                        single-line
                        hide-details
                    ></v-text-field>
                    <v-spacer></v-spacer>
                    
                    <v-dialog v-model="dialog" max-width="500px">
                        <template v-slot:activator="{ on }">
                            <v-btn color="primary" dark class="mb-2" v-on="on">New Todo</v-btn>
                        </template>
                        <v-card>
                            <v-card-title>
                                <span class="headline">{{ formTitle }}</span>
                            </v-card-title>

                            <v-card-text>
                                <v-container>
                                    <v-row>
                                        <v-col cols="12" sm="6" md="4">
                                            <v-text-field v-model="editedItem.title" label="Title"></v-text-field>
                                        </v-col>
                                        <v-col cols="12" sm="6" md="4">
                                            <v-text-field v-model="editedItem.description" label="Description"></v-text-field>
                                        </v-col>
                                        <v-col cols="12" sm="6" md="4">
                                            <v-text-field v-model="editedItem.deadline" label="Deadline"></v-text-field>
                                        </v-col>
                                        <v-col cols="12" sm="6" md="4">
                                            <v-text-field v-model="editedItem.status" label="Status"></v-text-field>
                                        </v-col>
                                    </v-row>
                                </v-container>
                            </v-card-text>

                            <v-card-actions>
                                <v-spacer></v-spacer>
                                <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
                                <v-btn color="blue darken-1" text @click="save">Save</v-btn>
                            </v-card-actions>
                        </v-card>
                    </v-dialog>
                </v-toolbar>
            </template>

            <template v-slot:item.actions="{ item }">
                <v-icon small class="mr-2" @click="editItem(item)">
                    mdi-pencil
                </v-icon>
                <v-icon small @click="deleteItem(item)">
                    mdi-delete
                </v-icon>
            </template>

            <template v-slot:no-data>
                <v-btn color="primary" @click="initialize">Reset</v-btn>
            </template>

            <template v-slot:expanded-item="{ headers, item }">
                <td :colspan="headers.length">More info about {{ item.title }}</td>
            </template>
        </v-data-table>
    </div>
</template>

<script>
export default {
    name: 'App',
    data: () => ({
        dialog: false,
        selected: [],
        search: '',
        expanded: [],
        singleExpand: false,
        headers: [
            {
                text: 'Title',
                align: 'start',
                sortable: true,
                value: 'title',
            },
            { text: 'Description', value: 'description' },
            { text: 'Deadline', value: 'deadline' },
            { text: 'Status', value: 'status' },
            { text: 'Actions', value: 'actions', sortable: false },
            { text: '', value: 'data-table-expand' },
        ],
        todos: [],
        editedIndex: -1,
        editedItem: {
            title: '',
            description: '',
            deadline: '',
            status: 1
        },
        defaultItem: {
            title: '',
            description: '',
            deadline: '',
            status: 1
        },
    }),
    computed: {
        formTitle () {
            return this.editedIndex === -1 ? 'New Todo' : 'Edit Todo'
        },
    },
    watch: {
        dialog (val) {
            val || this.close()
        },
    },
    created () {
        this.initialize()
    },
    methods: {
        initialize () {
            this.todos = [
                {
                    title: 'Task 1',
                    description: 'Description 1',
                    deadline: '10-10-2020',
                    status: 'Open'
                },
                {
                    title: 'Task 2',
                    description: 'Description 2',
                    deadline: '20-05-2020',
                    status: 'In progress'
                },
                {
                    title: 'Task 3',
                    description: 'Description 3',
                    deadline: '06-06-2020',
                    status: 'Closed'
                },
                {
                    title: 'Task 4',
                    description: 'Description 4',
                    deadline: '10-10-2020',
                    status: 'Closed'
                },
                {
                    title: 'Task 5',
                    description: 'Description 5',
                    deadline: '20-05-2020',
                    status: 'In progress'
                },
                {
                    title: 'Task 6',
                    description: 'Description 6',
                    deadline: '06-06-2020',
                    status: 'Open'
                },
            ]
        },
        editItem (item) {
            this.editedIndex = this.todos.indexOf(item)
            this.editedItem = Object.assign({}, item)
            this.dialog = true
        },
        deleteItem (item) {
            const index = this.todos.indexOf(item)
            confirm('Are you sure you want to delete this item?') && this.todos.splice(index, 1)
        },
        close () {
            this.dialog = false
            setTimeout(() => {
                this.editedItem = Object.assign({}, this.defaultItem)
                this.editedIndex = -1
            }, 300)
        },
        save () {
            if (this.editedIndex > -1) {
                Object.assign(this.todos[this.editedIndex], this.editedItem)
            } else {
                this.todos.push(this.editedItem)
            }
            this.close()
        },
    },
}
</script>

<style>
body {
    background: black;
    color: #fff;
}
</style>
