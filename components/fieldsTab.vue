<template>
    <div>
        <div>
          <div class="float-box float-right pb-1">
            <b-btn variant="outline-success" @click.prevent="openAddUserModal"><i class="fa fa-plus"></i> Add Field</b-btn>
          </div>
        </div>
        <div v-if="field_list.length > 0">
            <table class="table table-bordered">
                <thead class="thead-dark">
                    <tr>
                      <th>Name</th>
                      <th>Type</th>
                      <th>Value</th>
                      <th class="text-center">Action</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item, index) in field_list" :key="index">
                      <td style="font-size: 17pt;">{{item.attribute_name}}</td>
                      <td style="font-size: 17pt;">{{item.attribute_type}}</td>
                      <td style="font-size: 17pt;">{{item.attribute_value?item.attribute_value:'N/A'}}</td>
                   <!--   <td style="font-size: 17pt;">{{item.visibility?item.visibility:'N/A'}}</td> -->
                      <td class="text-center">
                          <b-btn variant="outline-warning" @click.prevent="openEditUserModal(item)"><i class="fa fa-pencil"></i></b-btn>
                          <b-btn variant="outline-danger" @click.prevent="OpenDeleteModal(item)"><i class="fa fa-trash"></i></b-btn>
                      </td>
                    </tr>
                </tbody>
            </table>
        </div>
        <div v-else>
            <table class="table">
                <thead>
                  <tr class="text-center">No users found</tr>
                </thead>
            </table>
        </div>
        <!-- check delete modal  -->
        <b-modal id="deleteUserModal" hide-footer hide-header>
          <h1 style="font-size: 16px;" class="p-3 text-center">Do you really want to delete the Filed?</h1>
          <div class="py-3 text-center">
            <b-btn class="m-1" variant="outline-danger" @click.prevent="deleteUser"><i class="fa fa-check"></i> Yes</b-btn>
            <b-btn class="m-1" variant="outline-primary" @click.prevent="dontDeleteUser"><i class="fa fa-times"></i> No</b-btn>
          </div>
        </b-modal>

        <!-- delete user assigned module modal  -->
        <b-modal id="deleteUserAssignedModuleModal" hide-footer hide-header>
          <h1 style="font-size: 16px;" class="p-3 text-center">Do you really want to delete the the module?</h1>
          <div class="py-3 text-center">
            <b-btn class="m-1" variant="outline-danger" @click.prevent="deleteUserAssignedModule"><i class="fa fa-check"></i> Yes</b-btn>
            <b-btn class="m-1" variant="outline-primary" @click.prevent="dontDeleteUserAssignedModule"><i class="fa fa-times"></i> No</b-btn>
          </div>
        </b-modal>

        <!-- assign user modal -->
        <b-modal id="addUserModuleModal" size="lg" hide-footer :title="add_user.Name">
          <div>
            <div class="flex-box float-right mb-3">
              <b-btn variant="outline-success" @click.prevent="openModalForAddingModulesForUser(add_user)"><i class="fa fa-plus"></i> Assign module</b-btn>
            </div>
            <div v-if="add_user && add_user.available_module">
              <table class="table table-bordered">
                <thead class="thead-dark">
                  <tr>
                    <th>Name</th>
                    <th>Permission</th>
                    <th class="text-center">Action</th>
                  </tr>
                </thead>
                <tbody>
                  <tr  v-for="element in add_user.modules" :key="element.id">
                    <td>{{element.name}}</td>
                    <td>
                      <b-form-select :options="permissions" v-model="element.module_permissions[0].permission"></b-form-select>
                    </td>
                    <td class="text-center">
                      <b-btn variant="outline-warning" @click.prevent="updateModulePermission(element.module_permissions[0])"><i class="fa fa-save"></i></b-btn>
                      <b-btn variant="outline-danger" @click.prevent="openDeleteUserAssignedModule(add_user, element)"><i class="fa fa-trash"></i></b-btn>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
            <div v-else class="text-center flex-box">
              <table class="table">
                <thead>
                  <tr class="text-center">No modules found</tr>
                </thead>
              </table>
            </div>
          </div>
        </b-modal>

        <b-modal id="addUserModal" hide-footer no-close-on-backdrop :title="addOrEdit == 'add' ? 'Create Filed' : 'Update Field'">
          <div class="wrapper text-center">
            <b-form-input placeholder="Name" v-model="add_field_name" style="width: 100%;" class="p-3 item mb-2"></b-form-input>
                <b-form-select placeholder="Field Type" v-model="add_filed_type" :options="field_type"></b-form-select>
              
            <b-form-input v-if="add_filed_type == 'Select Box'" placeholder="Value"  v-model="add_field_type_value" style="width: 100%;" class="p-3 item mb-2"></b-form-input>
            <!--<b-form-select placeholder="Value"  v-model="add_field_visibility"  :options="visibility"></b-form-select>-->
            <b-button variant="outline-success" class="p-2 item m-2 text-center" @click.prevent="addUpdateUsers"> <i :class="addOrEdit == 'add' ? 'fa fa-plus' : 'fa fa-save'"></i> {{addOrEdit == 'add' ? 'Create' : 'Update'}}</b-button>
            <b-button variant="outline-danger" @click.prevent="CloseAddUserModal">Close</b-button>
          </div>
        </b-modal>

        <b-modal id="addModuleToUserModal" no-close-on-backdrop hide-footer title="Assign module to user">
          <div>
            <div class="text-center">
              <div class="p-1">
                <b-form-select v-model="selectedModule" :options="module_list"></b-form-select>
              </div>
              <div class="p-1">
                <b-form-select v-model="selectedPermission" :options="permissions"></b-form-select>
              </div>
              <div class="p-2">
                <b-btn variant="outline-success" @click.prevent="addModuleForUser"><i class="fa fa-plus"></i> Save</b-btn>
                <b-btn variant="outline-danger" @click.prevent="CloseModuleAddingModal">Close</b-btn>
              </div>
            </div>
          </div>
        </b-modal>
    </div>
</template>


<script>
export default {
    name: 'FieldsTab',
    // props: ['add_user', 'users_list', 'permissions'],
    data() {
      return {
        module_list: [],
        add_field_name: null,
        add_filed_type:null,
        add_field_visibility:null,
        add_user: {},
        users_list: [],
        field_list: [],
        selectedUserForDelete: {},
        selectedModuleForDelete: null,
        selectedModuleForDeleteUser: null,
        addOrEdit: null,
        selectedModule: null,
        add_user_name: null,
        add_user_email: null,
        add_user_password: null,
        add_user_c_password: null,
        selectedPermission: null,
        selectedUser: null,
        visibility:[
            {
            value: null,
            text: 'Select a Value',
            disabled: true
          },
          {
            value: 'Visible',
            text: 'Visible',
          },
          {
            value: 'Hide',
            text: 'Hide',
          }
        ],
        field_type: [
          {
            value: null,
            text: 'Select a Type',
            disabled: true
          },{
            value: 'Text',
            text: 'Text'
          }, {
            value: 'Radio Button',
            text: 'Radio Button'
          }, {
            value: 'Check Box',
            text: 'Check Box'
          },
          {
            value: 'Select Box',
            text: 'Select Box'
          }
        ],
      }
    },
    mounted() {
      this.fetchUsers()
    },
    methods: {
      openDeleteUserAssignedModule(user, item) {
        this.selectedModuleForDelete = item.module_permissions[0].id
        this.selectedModuleForDeleteUser = user
        this.$bvModal.show('deleteUserAssignedModuleModal')
      },
      async deleteUserAssignedModule() {
        try {
          const resp = await this.$axios.post('/module-permission/delete', {id: this.selectedModuleForDelete})
          this.$bvToast.toast( resp.data.message , {
            variant: 'danger',
            solid: true
          })
          this.$bvModal.hide('deleteUserAssignedModuleModal')
          this.openAddModuleModal(this.selectedModuleForDeleteUser)
        } catch (error) {
          console.log(error);
        }
      },
      dontDeleteUserAssignedModule() {
        this.selectedModuleForDelete = null
        this.selectedModuleForDeleteUser = null
        this.$bvModal.hide('deleteUserAssignedModuleModal')
      },
      async updateModulePermission(item) {
        try {
          const resp = await this.$axios.post('/module-permission/edit', {
            id: item.id,
            permission: item.permission
          })
          if (resp.data.data.updated) {
            this.$bvToast.toast( resp.data.message , {
              variant: 'success',
              solid: true
            })
          } else {
            this.$bvToast.toast( resp.data.message , {
              variant: 'danger',
              solid: true
            })
          }S
        } catch (error) {
          console.log(error);
        }
      },
      async OpenDeleteModal(user) {
        try {
          const resp = await this.$axios.post(`/dynamic-attribute/delete`, { id: user.id, sure: null})
          if (!resp.data.data.deleted) {
            if (resp.data.data.cause == 1) {
              this.$bvToast.toast( resp.data.message , {
                variant: 'danger',
                solid: true
              })
              return
            }
          } 
          this.selectedUserForDelete = user.id
          this.$bvModal.show('deleteUserModal')     
        } catch (error) {
          console.log(error);
        }
      }, 
      CloseAddUserModal() {
        this.add_field_name=null
        this.add_filed_type = null
        this.add_field_type_value = null
        this.$bvModal.hide('addUserModal')
      },
      async deleteUser() {
        try {
          const resp = await this.$axios.post(`/dynamic-attribute/delete`, { id: this.selectedUserForDelete, sure: 'YES'})
          this.$bvToast.toast( resp.data.message , {
            variant: 'danger',
            solid: true
          })
          this.selectedUserForDelete = null
          this.fetchUsers()
          this.$bvModal.hide('deleteUserModal')
        } catch (error) {
          console.log(error);
        }
      },
      dontDeleteUser() {
        this.selectedUserForDelete = null
        this.fetchUsers()
        this.$bvModal.hide('deleteUserModal')
      },
      async addUpdateUsers() {
        try {
          if (this.addOrEdit == 'add') {
            if (this.add_user_password == this.add_user_c_password) {
              const payload = {
                attribute_name: this.add_field_name,
                attribute_type: this.add_filed_type,
                attribute_value: this.add_field_type_value,
              }
              const resp = await this.$axios.post('/dynamic-attribute/add', payload)
              if (resp.data.data.created) {
                this.$bvToast.toast( resp.data.message , {
                  variant: 'success',
                  solid: true
                })
                this.add_field_name=null
                this.add_filed_type = null
                this.add_field_type_value = null
                this.fetchUsers()
                this.$bvModal.hide('addUserModal')
              } else {
                this.$bvToast.toast( resp.data.message , {
                  variant: 'danger',
                  solid: true
                })
                this.add_user_name = null
                this.add_user_email = null
                this.add_user_c_password = null
                this.add_user_password = null
                this.addOrEdit = null
                this.fetchUsers()
                this.$bvModal.hide('addUserModal')
              }
            } else {
              this.$bvToast.toast( 'Password and confirm password must be same. Please try again!!' , {
                variant: 'danger',
                solid: true
              })
              this.add_user_password = null
              this.add_user_c_password = null
            }
          } else {
            const payload = {
              id: Number(this.addOrEdit.replace('edit ', '')),
              attribute_name: this.add_field_name,
              attribute_type: this.add_filed_type,
              attribute_value: this.add_field_type_value,
            }
            const resp = await this.$axios.post('/dynamic-attribute/edit', payload)
            if (resp.data.data.update) {
              this.$bvToast.toast( resp.data.message , {
                variant: 'success',
                solid: true
              })
              this.add_user_name = null
              this.addOrEdit = null
              this.fetchUsers()
              this.$bvModal.hide('addUserModal')
            } else {
              this.$bvToast.toast( resp.data.message , {
                variant: 'danger',
                solid: true
              })
              this.add_user_name = null
              this.addOrEdit = null
              this.fetchUsers()
              this.$bvModal.hide('addUserModal')
            }
          }
        } catch (error) {
          console.log(error);
        }
      },
      openEditUserModal(item) {
        this.addOrEdit = 'edit '+item.id
        this.add_field_name=item.attribute_name
        this.add_filed_type = item.attribute_type
        this.add_field_type_value = item.attribute_value
        this.$bvModal.show('addUserModal')
      },
      openAddUserModal() {
        this.addOrEdit = 'add'
        this.$bvModal.show('addUserModal')
      },
      async openAddModuleModal(item) {
        this.add_user = {...item}
        const resp = await this.$axios.get(`/module/fetch-for-user/${item.id}`)
        let i = resp.data.data.module
        let modules = i
        this.add_user = {
          ...this.add_user,
          available_module: modules.length > 0,
          modules
        }
        this.$bvModal.show('addUserModuleModal')
      },
      async fetchUsers() {
        try {
          const resp = await this.$axios.get('dynamic-attribute/fetch-all')
          this.field_list = resp.data.data.DynamicAttributes
          console.log(this.field_list)
        } catch (error) {
          console.log(error);
        }
      },
      async openModalForAddingModulesForUser(user) {
        try {
          this.selectedUser = user
          const resp = await this.$axios.get('module/fetch')
          const modules = await this.$axios.get(`/module/fetch-for-user/${user.id}`)
          this.module_list = resp.data.data.data.map(e => {
            const available = modules.data.data.module.filter(item => item.name == e.name)
            return {
              value: {user_id: user, module_id: e.id},
              text: e.name,
              disabled: available.length > 0 ? true : false
            }
          })
          this.module_list.unshift({
            value: null,
            text: 'Select a module.',
            disabled: true
          })
          this.$bvModal.show('addModuleToUserModal')
        } catch (error) {
          console.log(error);
        }
      },
      CloseModuleAddingModal() {
        this.selectedModule = null
        this.selectedPermission = null
        this.selectedUser = null
        this.$bvModal.hide('addModuleToUserModal')
      },
      async addModuleForUser() {
        try {
          if (this.selectedModule && this.selectedPermission) {
            const payload = {
              module_id: this.selectedModule.module_id,
              user_id: this.selectedUser.id,
              permission: this.selectedPermission
            }
            const resp = await this.$axios.post('/module-permission/add', payload)
            if (resp.data.data.created) {
              this.$bvToast.toast( resp.data.message , {
                variant: 'success',
                solid: true
              })
            } else {
              this.$bvToast.toast( resp.data.message , {
                variant: 'danger',
                solid: true
              })
            }
            await this.openAddModuleModal(this.selectedUser)
            this.CloseModuleAddingModal()
          } else {
            if (!this.selectedModule) {
              this.$bvToast.toast( 'Select a module.' , {
                variant: 'warning',
                solid: true
              })
            } 
            if (!this.selectedPermission) {
              this.$bvToast.toast( 'Select a permission for the module.' , {
                variant: 'warning',
                solid: true
              })
            }
          }          
        } catch (error) {
          console.log(error);
        }
      }
    }
}
</script>