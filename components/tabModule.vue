<template>
    <div v-if="this.$store.state.auth.user.role == 'SUPER'">
        <div v-if="index < length - 2">
            <!-- modules  -->
            <div v-for="(eachModule, i) in allTabs" :key="i">
                <!-- <pre>{{eachModule}}</pre> -->
                <div v-if="i+1 == index">
                    <div>
                        <table class="table">
                            <tr >
                                <th class="col-2">
                                    <h3>{{eachModule.name}}</h3>
                                    <p>Assigned user count: {{eachModule.module_permissions.length}}</p>
                                </th>
                                <th class="table-part col-8">
                                    <div class="button-container-module">
                                        <b-btn variant="outline-success" @click="addTaskForModule(eachModule.id)"><i class="fa fa-plus"></i> Add Entity</b-btn>
                                    </div>
                                    <b-modal id="bv-modal-example" hide-footer hide-header no-close-on-backdrop>
                                        <div class="container-add-task-modal">
                                            <h6 class="text-center">{{add_task.id ? 'Edit Task' : 'Add Task'}}</h6>
                                            <b-form-input placeholder="Enter the task name" v-model="add_task.attribute_name"></b-form-input>
                                            <b-form-select :options="task_types" v-model="add_task.attribute_type"></b-form-select>
                                            <div v-if="add_task.attribute_type == 'Select Box' || add_task.attribute_type == 'Radio Button'">
                                                <div v-for="(e, i) in add_task_select_values" :key="i">
                                                    <div class="selected-items">
                                                        <div>
                                                            {{e}}
                                                            <i class="fa fa-trash" id="delete-select" @click.prevent="deleteElement(i)" aria-hidden="true"></i>
                                                        </div>
                                                    </div>
                                                </div>
                                                <b-form-input placeholder="Enter the values for select box" v-model="add_task.attribute_value" @keypress="checkEnter($event)"></b-form-input>
                                            </div>
                                        </div>
                                        <div class="text-center">
                                            <b-btn class="mt-3" variant="outline-success" @click.prevent="saveTask"> <i class="fa fa-check"></i>{{add_task.id ? ' Update' : ' Save'}}</b-btn>
                                            <b-btn class="mt-3" @click="closeModal" variant="outline-danger"> <i class="fa fa-close"></i> Close</b-btn>
                                        </div>
                                    </b-modal>
                                    <div class="button-container-module">
                                        <b-btn variant="outline-success" @click.prevent="addAvailable(eachModule.id)"><i class="fa fa-plus"></i> Add Available Entity</b-btn>
                                    </div>
                                     <div>
                                        <b-modal id="bv-modal-example-add-available" hide-footer hide-header no-close-on-backdrop>
                                            <b-select
                                                v-model="selected_entity"
                                                :options="available_entities"
                                            ></b-select>
                                            <div class="text-center">
                                                <b-btn class="mt-3" variant="outline-success" @click.prevent="saveEntity"> <i class="fa fa-check"></i> Save</b-btn>
                                                <b-btn class="mt-3" @click="closeModal4" variant="outline-danger"> <i class="fa fa-close"></i> Close</b-btn>
                                            </div>
                                        </b-modal>
                                    </div>
                                    <div class="button-container-module">
                                        <b-btn @click.prevent="openAssignModal(eachModule.id)" variant="outline-success">Assign to user</b-btn>
                                    </div>
                                    <div>
                                        <b-modal id="bv-modal-example-assign-user" hide-footer hide-header no-close-on-backdrop>
                                            <b-form-select @select="selected_user" v-model="assign_user.user_id" :options="assignUserUserList"></b-form-select>
                                            <b-form-select v-model="assign_user.permission" :options="permissions"></b-form-select>
                                            <div class="text-center">
                                                <b-btn class="mt-3" variant="outline-success" @click.prevent="AssignUser"> <i class="fa fa-check"></i> Save</b-btn>
                                                <b-btn class="mt-3" @click="closeModal2" variant="outline-danger"> <i class="fa fa-close"></i> Close</b-btn>
                                            </div>
                                        </b-modal>
                                    </div>
                                    <div class="button-container-module">
                                        <b-btn @click.prevent="openAssignEditModal(eachModule.id)" variant="outline-success">Edit assignments</b-btn>
                                    </div>
                                    <div>
                                        <b-modal id="bv-modal-example-assign-edit-user" hide-footer hide-header no-close-on-backdrop>
                                            <div>
                                                <!-- <pre>{{assignUserList}}</pre> -->
                                                <div>
                                                    <div v-if="assignUserList.length > 0">
                                                        <div class="row" v-for="(e,i) in assignUserList" :key="i">
                                                            <h6 class="content1 data">{{e.user_name}}</h6>
                                                            <b-form-select class="content2 data" @change="updateUserPermission(e)" v-model="e.permission" :options="permissions"></b-form-select>
                                                            <div class="content3 text-center">
                                                                <span class="text-center">
                                                                    <b-btn variant="outline-danger" @click.prevent="deleteUserPermission(e.id)">
                                                                        <i class="fa fa-trash"></i> Delete
                                                                    </b-btn>
                                                                </span>
                                                            </div>
                                                        </div>
                                                    </div>
                                                    <div v-else>
                                                        <h3>No records found.</h3>
                                                    </div>
                                                </div>
                                            </div>
                                            <div class="text-center">
                                                <b-btn class="mt-3" @click="closeModal3" variant="outline-danger"> <i class="fa fa-close"></i> Close</b-btn>
                                            </div>
                                        </b-modal>
                                    </div>
                                </th>
                            </tr>
                        </table>
                    </div>
                    <div v-if="eachModule.dynamic_attributes.length > 0">
                        <table class="table">
                            <thead>
                                <tr>
                                    <th>Name</th>
                                    <th class="text-center">Type</th>
                                    <th class="text-center">Action</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(DynaAtt, i) in eachModule.dynamic_attributes" :key="i">
                                    <td>
                                        <h6>{{DynaAtt.attribute_name}}</h6>
                                    </td>
                                    <td style="text-align: center;">
                                        <span v-if="DynaAtt.attribute_type == 'Text'">
                                            <b-form-input :placeholder="DynaAtt.attribute_name"></b-form-input>
                                        </span>
                                        <span v-if="DynaAtt.attribute_type == 'Check Box'">
                                            <b-form-checkbox>{{DynaAtt.attribute_name}}</b-form-checkbox>
                                        </span>
                                        <div v-if="DynaAtt.attribute_type == 'Select Box'">
                                            Select an option: <b-form-select :options="DynaAtt.attribute_value.split(',')"></b-form-select>
                                        </div>
                                        <div v-if="DynaAtt.attribute_type == 'Radio Button'">
                                            <b-form-radio-group
                                                v-model="selected"
                                                :name="DynaAtt.attribute_name"
                                                :options="DynaAtt.attribute_value.split(',')"
                                            ></b-form-radio-group>
                                        </div>
                                    </td>
                                    <td class="text-center">
                                        <b-btn variant="outline-primary" @click.prevent="openTaskModalEdit(DynaAtt)"><i class="fa fa-pencil"></i></b-btn>
                                        <b-btn variant="outline-danger" @click.prevent="deleteTask(DynaAtt.id)"><i class="fa fa-trash"></i></b-btn>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    <div v-else class="text-center">
                        <h3>No task found.</h3>
                    </div>
                </div>
            </div>
        </div>
        <div v-else-if="index == length - 2" class="text-center m-3 ">
            <!-- add module -->
            <div class="wrapper">
                <h3 class="item">CREATE MODULE</h3>
                <b-form-input placeholder="Name" class="p-3 item m-2" style="width: max-content;" v-model="add_module_name"></b-form-input>
                <b-button variant="outline-success" class="p-2 m-2 item text-center" @click.prevent="addModule"> <i class="fa fa-plus"></i> Create</b-button>
            </div>
        </div>
        <div v-else-if="index == length - 1" class="text-center m-3">
            <!-- view user -->
            <h3>VIEW USER</h3>
            <div v-for="(item, i) in users_list" :key="i">
                <div class="item2">
                    <b-card
                        :title="item.name"
                        style="width: 12.5rem;"
                        class="mb-2"
                    >
                        <b-card-text>
                            {{item.email}}
                        </b-card-text>
                    </b-card>
                    <i id="cross" @click.prevent="deleteUser(item)" class="fa fa-times"></i>
                </div>
            </div>
        </div>
        <div v-else class="text-center m-3">
            <!-- Add user -->
            <div class="wrapper">
                <h3 class="item">CREATE USER</h3>
                <b-form-input placeholder="Name" v-model="add_user_name" style="width: max-content;" class="p-3 item m-2"></b-form-input>
                <b-form-input placeholder="Email" v-model="add_user_email" style="width: max-content;" class="p-3 item m-2"></b-form-input>
                <b-form-input placeholder="Password" type="password" v-model="add_user_password" style="width: max-content;" class="p-3 item m-2"></b-form-input>
                <b-form-input placeholder="Confirm Password" type="password" v-model="add_user_c_password" style="width: max-content;" class="p-3 item m-2"></b-form-input>
                <b-button variant="outline-success" class="p-2 item m-2 text-center" @click.prevent="addUsers"> <i class="fa fa-plus"></i> Create</b-button>
            </div>
        </div>
    </div>
    <div v-else>
        <div>
            <!-- modules  -->
            <div v-for="(eachModule, i) in allTabs" :key="i">
                <!-- <pre>{{eachModule}}</pre> -->
                <div v-if="i+1 == index">
                    <div>
                        <!-- <pre>{{eachModule}}</pre> -->
                        <table class="table">
                            <tr >
                                <th>
                                    <h3>{{eachModule.module_name}}</h3>
                                    <span v-if="eachModule.module_permission == 'EDIT'">
                                        <p>Permission: {{eachModule.module_permission }} <i class="fa fa-pencil"></i></p>
                                    </span>
                                    <span v-if="eachModule.module_permission == 'VIEW'">
                                        <p>Permission: {{eachModule.module_permission }} <i class="fa fa-eye"></i></p>
                                    </span>
                                    <span v-if="eachModule.module_permission == 'ALL'">
                                        <p>Permission: {{eachModule.module_permission }} <i class="fa fa-universal-access"></i></p>
                                    </span>
                                </th>
                                <th class="table-part" v-if="eachModule.module_permission == 'ALL'">
                                    <div class="button-container-module">
                                        <b-btn variant="outline-success" @click="addTaskForModule(eachModule.module_id)"><i class="fa fa-plus"></i> Add Task</b-btn>
                                    </div>
                                    <b-modal id="bv-modal-example" hide-footer hide-header no-close-on-backdrop>
                                        <div class="container-add-task-modal">
                                            <h6 class="text-center">{{add_task.id ? 'Edit Task' : 'Add Task'}}</h6>
                                            <b-form-input placeholder="Enter the task name" v-model="add_task.attribute_name"></b-form-input>
                                            <b-form-select :options="task_types" v-model="add_task.attribute_type"></b-form-select>
                                            <div v-if="add_task.attribute_type == 'Select Box' || add_task.attribute_type == 'Radio Button'">
                                                <div v-for="(e, i) in add_task_select_values" :key="i">
                                                    <div class="selected-items">
                                                        <div>
                                                            {{e}}
                                                            <i class="fa fa-trash" id="delete-select" @click.prevent="deleteElement(i)" aria-hidden="true"></i>
                                                        </div>
                                                    </div>
                                                </div>
                                                <b-form-input placeholder="Enter the values for select box" v-model="add_task.attribute_value" @keypress="checkEnter($event)"></b-form-input>
                                            </div>
                                        </div>
                                        <div class="text-center">
                                            <b-btn class="mt-3" variant="outline-success" @click.prevent="saveTask"> <i class="fa fa-check"></i>{{add_task.id ? ' Update' : ' Save'}}</b-btn>
                                            <b-btn class="mt-3" @click="closeModal" variant="outline-danger"> <i class="fa fa-close"></i> Close</b-btn>
                                        </div>
                                    </b-modal>
                                </th>
                            </tr>
                        </table>
                    </div>
                    <div>
                        <table class="table">
                            <thead>
                                <tr>
                                    <th>Name</th>
                                    <th class="text-center">Options</th>
                                    <th class="actions text-center">Action</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(DynaAtt, i) in eachModule.module.dynamic_attributes" :key="i">
                                    <td>
                                        <h6>{{DynaAtt.attribute_name}}</h6>
                                    </td>
                                    <td style="text-align: center;">
                                        <!-- <pre>{{DynaAtt}}</pre> -->
                                        <span v-if="DynaAtt.attribute_type == 'Text'">
                                            <b-form-input v-model="DynaAtt.attribute_value" :disabled="eachModule.module_permission == 'VIEW'? true : false" :placeholder="DynaAtt.attribute_name"></b-form-input>
                                        </span>
                                        <span v-else-if="DynaAtt.attribute_type == 'Check Box'">
                                            <b-form-checkbox v-model="DynaAtt.attribute_value" :disabled="eachModule.module_permission == 'VIEW'? true : false">{{DynaAtt.attribute_name}}</b-form-checkbox>
                                        </span>
                                        <div v-else-if="DynaAtt.attribute_type == 'Select Box'">
                                            Select an option: <b-form-select v-model="DynaAtt.attribute_value"  :disabled="eachModule.module_permission == 'VIEW'? true : false" :options="DynaAtt.options"></b-form-select>
                                        </div>
                                        <div v-else-if="DynaAtt.attribute_type == 'Radio Button'">
                                            <!-- <pre>{{DynaAtt}}</pre> -->
                                            <b-form-radio-group
                                                v-model="DynaAtt.attribute_value"
                                                :name="DynaAtt.attribute_name"
                                                :options="DynaAtt.options"
                                            ></b-form-radio-group>
                                        </div>
                                    </td>
                                    <td class="text-center">
                                        <div v-if="eachModule.module_permission == 'ALL'">
                                            <b-btn variant="outline-success" @click.prevent="saveDynaAttVal(DynaAtt)"><i class="fa fa-check"></i></b-btn>
                                            <b-btn variant="outline-primary" @click.prevent="openTaskModalEdit(DynaAtt)"><i class="fa fa-pencil"></i></b-btn>
                                            <b-btn variant="outline-danger" @click.prevent="deleteTask(DynaAtt.id)"><i class="fa fa-trash"></i></b-btn>
                                        </div>
                                        <div v-else>
                                            <b-btn :disabled="eachModule.module_permission == 'VIEW'? true : false" variant="outline-success" @click.prevent="saveDynaAttVal(DynaAtt)"><i class="fa fa-check"></i></b-btn>
                                        </div>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name: 'EachTab',
    props: ['index', 'length', 'allTabs'],
    data() {
        return {
            add_module_name: null,
            users_list: [],
            add_user_name: null,
            add_user_email: null,
            add_user_password: null,
            add_user_c_password: null,
            selected_entity: null,
            available_entities: [],
            selected: '',
            add_task: {
                id: null,
                module_id: null,
                attribute_name: null,
                attribute_type: null,
                attribute_value: null,
            },
            assign_user: {
                module_id: null,
                user_id: null,
                permission: null,
            },
            assignUserUserList: [],
            permissions: [
                {
                    value: null,
                    text: 'Please select a permission',
                    disabled: true
                },
                {
                    value: 'EDIT',
                    text: 'EDIT'
                }, {
                    value: 'VIEW',
                    text: 'VIEW'
                }, {
                    value: 'ALL',
                    text: 'ALL'
                }
            ],
            assignUserList: [],
            add_task_select_values: [],
            task_types: [
                {
                    value: null,
                    text: 'Please select an option',
                    disabled: true
                },
                {
                    value: 'Text',
                    text: 'Text',
                },
                {
                    value: 'Check Box',
                    text: 'Check Box'
                },
                {
                    value: 'Select Box',
                    text: 'Select Box'
                },
                {
                    value: 'Radio Button',
                    text: 'Radio Button'
                }
            ]
        }
    },
    mounted() {
        this.fetchUsers()
    },
    watch: {
        index() {
            this.setDefault()
            this.fetchUsers()
        },
        add_task(val) {
            if (val.module_id == null) {
                this.add_task_select_values = []
            }
        }
    },
    methods: {
        async saveEntity() {
            try {
                if (this.selected_entity) {
                    if(!this.selected_entity.attribute_name) {
                        this.$bvToast.toast('Enter attribute name.', {
                            variant: 'danger',
                            solid: true,
                        })
                        return
                    }
                    if (!this.selected_entity.attribute_type) {
                        this.$bvToast.toast('Enter attribute type.', {
                            variant: 'danger',
                            solid: true,
                        })
                        return
                    }
                    if (this.selected_entity.attribute_type == 'Select Box' && !this.selected_entity.attribute_value) {
                        this.$bvToast.toast('Enter attribute value.', {
                            variant: 'danger',
                            solid: true,
                        })
                        return
                    }
                    const resp = await this.$axios.post('dynamic-attribute/add', this.selected_entity)
                    if (resp.data.message.includes('success')) {
                        this.$bvToast.toast('Dynamic attribute created successfully.', {
                            variant: 'success',
                            solid: true,
                        })
                    } else {
                        this.$bvToast.toast(resp?.data?.message.replace(resp?.data?.message[0], resp?.data?.message[0].toUpperCase()), {
                            variant: 'danger',
                            solid: true,
                        })
                    }
                    this.closeModal4()
                    this.$emit('reloadModule', {})
                }
            } catch (error) {
                console.log(error);
            }
        },
        closeModal4() {
            this.selected_entity = null
            this.$bvModal.hide('bv-modal-example-add-available')
        },
        async addAvailable(module_id) {
            try {
                const resp = await this.$axios.get('dynamic-attribute/fetch-all')
                let added = []
                let data = resp.data.data.DynamicAttributes.map(e => {
                    let val = `${e.attribute_name} (${e.attribute_type})`
                    if (!added.includes(val)) {
                        added.push(val)
                        return {
                            value: {
                                module_id: module_id,
                                attribute_name: e.attribute_name,
                                attribute_type: e.attribute_type,
                                attribute_value: e.attribute_value,
                            },
                            text: `${e.attribute_name} (${e.attribute_type})`
                        }
                    }
                    else return
                })
                let mappedData= await Promise.all(data)
                mappedData.unshift({
                    value: null,
                    text: 'Select an available entity.',
                    disabled: true
                })
                mappedData = mappedData.filter(item => item);
                this.available_entities = mappedData
                this.$bvModal.show('bv-modal-example-add-available')
            } catch (error) {
                console.log(error);
            }
        },
        async saveDynaAttVal(DynaAtt) {
            const payload = {
                attribute_id: DynaAtt.id,
                value: `${DynaAtt.attribute_value}`
            }
            const resp = await this.$axios.post('dynamic-attribute-value/add', payload)
            if (resp.data.message.includes('success')) {
                this.$bvToast.toast(resp?.data?.message+'.', {
                    variant: 'success',
                    solid: true,
                })
            } else {
                this.$bvToast.toast(resp?.data?.message, {
                    variant: 'danger',
                    solid: true,
                })
            }
        },
        async deleteTask(id) {
            const payload = {
                id
            }
            const resp = await this.$axios.post('dynamic-attribute/delete', payload)
            if (resp.data.message.includes('success')) {
                this.$bvToast.toast('Task deleted successfully.', {
                    variant: 'warning',
                    solid: true,
                })
            } else {
                this.$bvToast.toast(resp?.data?.message, {
                    variant: 'danger',
                    solid: true,
                })
            }
            this.$emit('reloadModule', {})
        },
        openTaskModalEdit(dynamic) {
            this.add_task = {
                id: dynamic.id,
                module_id: dynamic.module_id,
                attribute_name: dynamic.attribute_name,
                attribute_type: dynamic.attribute_type,
                attribute_value: dynamic.attribute_value
            }
            this.$bvModal.show('bv-modal-example') 
        },
        async deleteUserPermission(id) {
            const payload = {
                id
            }
            const resp = await this.$axios.post('module-permission/delete', payload)
            if (resp.data.message.includes('Success')) {
                this.$bvToast.toast(resp?.data?.message, {
                    variant: 'warning',
                    solid: true,
                })
            } else {
                this.$bvToast.toast(resp?.data?.message, {
                    variant: 'danger',
                    solid: true,
                })
            }
            this.closeModal3()
        },
        async updateUserPermission(payload) {
            delete payload.user_name
            const resp = await this.$axios.post('module-permission/edit', payload)
            if (resp.data.message.includes('Success')) {
                this.$bvToast.toast(resp?.data?.message, {
                    variant: 'success',
                    solid: true,
                })
            } else {
                this.$bvToast.toast(resp?.data?.message, {
                    variant: 'danger',
                    solid: true,
                })
            }
        },
        async AssignUser() {
            try {
                if (this.assign_user) {
                    if (!this.assign_user.user_id) {
                        this.$bvToast.toast('Select an user.', {
                            variant: 'danger',
                            solid: true,
                        })
                        return
                    }
                    if (!this.assign_user.permission) {
                        this.$bvToast.toast('Select a permission.', {
                            variant: 'danger',
                            solid: true,
                        })
                        return
                    }
                }
                const resp = await this.$axios.post('module-permission/add', this.assign_user)
                if (resp.data.message.includes('Successfully')) {
                    this.$bvToast.toast(resp?.data?.message, {
                        variant: 'success',
                        solid: true,
                    })
                } else {
                    this.$bvToast.toast(resp?.data?.message, {
                            variant: 'danger',
                            solid: true,
                        })
                }
                this.closeModal2()
                this.$emit('reloadModule', {})
            } catch (error) {
                console.log(error);
            }
        },
        closeModal2() {
            this.assign_user = {
                module_id: null,
                user_id: null,
                permission: null,
            }
            this.$bvModal.hide('bv-modal-example-assign-user')
        },
        closeModal3() {
            this.$emit('reloadModule', {})
            this.$bvModal.hide('bv-modal-example-assign-edit-user')
        },
        openAssignEditModal(id) {
            this.assignUserList = []
            this.users_list.forEach(e => {
                const available = this.allTabs[this.index - 1].module_permissions.filter(el => el.user_id == e.id && el.module_id == id)
                if (available.length > 0) {
                    this.assignUserList.push({
                        user_name: e.name,
                        user_id: e.id,
                        module_id: id,
                        id: available[0].id,
                        permission: available[0].permission
                    })
                }
            })
            this.$bvModal.show('bv-modal-example-assign-edit-user') 
        },
        openAssignModal(id) {
            this.assign_user.module_id = id
            this.assignUserUserList = this.users_list.map(e => {
                const available = this.allTabs[this.index - 1].module_permissions.filter(el => el.user_id == e.id && el.module_id == id)
                if (available.length > 0) {
                    return {
                        value: e.id,
                        text: e.name,
                        disabled: true
                    }
                }
                return {
                    value: e.id,
                    text: e.name
                }
            })
            this.assignUserUserList.unshift({
                value: null,
                text: 'Please select an user.',
                disabled: true
            })
            this.$bvModal.show('bv-modal-example-assign-user') 
        },
        async saveTask() {
            try {
                if (this.add_task) {
                    if (this.add_task.id) {
                        this.add_task.attribute_value = this.add_task.attribute_type == 'Select Box' || this.add_task.attribute_type == 'Radio Button' ? this.add_task_select_values?.join(',') : null
                        if(!this.add_task.attribute_name) {
                            this.$bvToast.toast('Enter attribute name.', {
                                variant: 'danger',
                                solid: true,
                            })
                            return
                        }
                        if (!this.add_task.attribute_type) {
                            this.$bvToast.toast('Enter attribute type.', {
                                variant: 'danger',
                                solid: true,
                            })
                            return
                        }
                        if (this.add_task.attribute_type == 'Select Box' || this.add_task.attribute_type == 'Radio Button' && !this.add_task.attribute_value) {
                            this.$bvToast.toast('Enter attribute value.', {
                                variant: 'danger',
                                solid: true,
                            })
                            return
                        }
                        const resp = await this.$axios.post('dynamic-attribute/edit', this.add_task)
                        if (resp.data.message.includes('success')) {
                            this.$bvToast.toast('Dynamic attribute updated successfully.', {
                                variant: 'success',
                                solid: true,
                            })
                        } else {
                            this.$bvToast.toast(resp?.data?.message.replace(resp?.data?.message[0], resp?.data?.message[0].toUpperCase()), {
                                variant: 'danger',
                                solid: true,
                            })
                        }
                        this.closeModal()
                        this.$emit('reloadModule', {})
                    } else {
                        this.add_task.attribute_value = this.add_task.attribute_type == 'Select Box' || this.add_task.attribute_type == 'Radio Button' ? this.add_task_select_values?.join(',') : null
                        if(!this.add_task.attribute_name) {
                            this.$bvToast.toast('Enter attribute name.', {
                                variant: 'danger',
                                solid: true,
                            })
                            return
                        }
                        if (!this.add_task.attribute_type) {
                            this.$bvToast.toast('Enter attribute type.', {
                                variant: 'danger',
                                solid: true,
                            })
                            return
                        }
                        if (this.add_task.attribute_type == 'Select Box' && !this.add_task.attribute_value) {
                            this.$bvToast.toast('Enter attribute value.', {
                                variant: 'danger',
                                solid: true,
                            })
                            return
                        }
                        delete this.add_task.id
                        const resp = await this.$axios.post('dynamic-attribute/add', this.add_task)
                        if (resp.data.message.includes('success')) {
                            this.$bvToast.toast('Dynamic attribute created successfully.', {
                                variant: 'success',
                                solid: true,
                            })
                        } else {
                            this.$bvToast.toast(resp?.data?.message.replace(resp?.data?.message[0], resp?.data?.message[0].toUpperCase()), {
                                variant: 'danger',
                                solid: true,
                            })
                        }
                        this.closeModal()
                        this.$emit('reloadModule', {})
                    }
                }
                
            } catch(err) {
                console.log(err);
            }
        },
        deleteElement(i) {
            this.add_task_select_values.splice(i, 1)
        },
        checkEnter(event) {
            if (event.charCode == 13) {
                this.add_task_select_values.push(this.add_task.attribute_value)
                this.add_task.attribute_value = null
            }
        },
        closeModal() {
            this.add_task = {
                id: null,
                module_id: null,
                attribute_name: null,
                attribute_type: null,
                attribute_value: null,
            }
            this.add_task_select_values = []
            this.$bvModal.hide('bv-modal-example')
        },
        selectedTab(num) {
            this.module_tabs.forEach(i => {
                if (i.number == num) {
                    this.module_tabs_current = num
                }
            })
        },
        async deleteUser(user) {
            this.$bvToast.toast('This feature will be enabled shortly.', {
                variant: 'danger',
                solid: true,
            })
        },
        async addUsers() {
            try {
                if (this.add_user_password && this.add_user_c_password && this.add_user_password == this.add_user_c_password && this.add_user_name && this.add_user_email) {
                    const payload = {
                        name: this.add_user_name,
                        email: this.add_user_email,
                        password: this.add_user_password
                    }
                    const resp = await this.$axios.post('user/add', payload)
                    if (resp?.data?.message.includes('success')) {
                        this.$bvToast.toast(resp?.data?.message, {
                            variant: 'success',
                            solid: true,
                        })
                    } else {
                        this.$bvToast.toast(resp?.data?.message, {
                            variant: 'danger',
                            solid: true,
                        })
                    }
                    this.setDefault()
                } else if(!this.add_user_password || !this.add_user_c_password || !this.add_user_name || !this.add_user_email) {
                    this.$bvToast.toast('Fill all the fields.', {
                        variant: 'danger',
                        solid: true,
                    })
                } else {
                    this.$bvToast.toast('Password and confirm password has to be same.', {
                        variant: 'danger',
                        solid: true,
                    })
                }
                this.fetchUsers()
            } catch (error) {
                console.log(error);
            }
        },
        async fetchUsers() {
            try {
                const resp = await this.$axios.get('user/list')
                this.users_list = resp.data.data.user
            } catch (error) {
                console.log(error);
            }
        },
        setDefault() {
            this.add_module_name = null
            this.add_user_name = null
            this.add_user_email = null
            this.add_user_password = null
            this.add_user_c_password = null
        },
        addTaskForModule(id) {
            this.add_task.module_id = id
            this.$bvModal.show('bv-modal-example') 
        },
        async addModule() {
            try {
                if (this.add_module_name) {
                    const res = await this.$axios.post('module/add', {
                        name: this.add_module_name
                    })
                    this.$bvToast.toast('Module added successfully.', {
                        variant: 'success',
                        solid: true,
                    })
                    this.add_module_name = null
                    this.$emit('reloadModule', {})
                    return
                } else {
                    this.$bvToast.toast('Please enter a name for the module.', {
                        variant: 'danger',
                        solid: true,
                    })
                    return
                }
            } catch (error) {
                console.log(error);
            }
        },
    }
}
</script>

<style scoped>
@import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css');
.wrapper {
  display: grid;
}
.item {
  align-self: center;
  justify-self: center;
}
.item2{
    display: flex;
    float: left;
}
#cross {
    display: absolute;
    cursor: pointer;
    height: 20px;
    width: 20px;
    color: rgb(255, 60, 0);
    transform: translateX(-20px);
    padding: 4px;
}
.tabs {
    display: flex; 
    float: left; 
    padding: 2px;
    cursor: pointer;
    margin-left: 5px;
    color: rgb(37, 150, 190);
}
.tabs :hover {
    color: rgb(0, 110, 255);
}

.details {
    margin-top: 10px;
    width: 100%;
    height: max-content;
}
.actions{
    min-width: 150px;
}
.table {
    width: 100%;
    border: none;
}
.table-part {
    padding: 10px;
    float: right;
}
.button-container-module {
    display: flex;
    float: right;
    padding: 3px;
    right: 0;
}
.selected-items {
    width: max-content;
    padding: 7px;
    margin: 5px;
    border-radius: 10px;
    display: flex;
    float: left;
    border: 1px solid black;
}

#delete-select {
    cursor: pointer;
    color: red;
}

.content1 {
    display: flex;
    float: left;
    width: 50%;
}

.content2 {
    display: flex;
    float: left;
    width: 30%;
}
.content3 {
    display: flex;
    float: left;
    width: 20%;
}

.heading{
    padding: 5px;   
    font-size: 1em;
    font-weight: 900;
}

.row {
    padding: 5px;
}

.data{
    text-align: center;
}
</style>