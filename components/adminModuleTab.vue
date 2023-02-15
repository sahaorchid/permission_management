<template>
    <div>
        <div class="w-100">
          <div class="float-box float-right m-3">
            <b-btn variant="outline-success" @click.prevent="openAddModuleModal"><i class="fa fa-plus"></i> Add module</b-btn>
          </div>
          <div class="float-box float-right m-3">
            <b-btn variant="outline-success" @click.prevent="openAddFieldsInModuleModal"><i class="fa fa-plus"></i> Add field in module</b-btn>
          </div>
        </div>
        <div v-if="module_list.length > 0">
          <table class="table table-bordered">
            <thead class="thead-dark">
              <tr class="col-12">
                <th class="col-5">Name</th>
                <th class="col-3 text-center">Fields Count</th>
                <th class="col-2 text-center">User Count</th>
                <th class="col-2 text-center">Action</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, index) in module_list" :key="index">
                <td style="font-size: 17pt;" class="col-5"><b-link @click.prevent="OpenUserListModal(item)">{{item.name}}</b-link></td>
                <td style="font-size: 17pt;" class="col-2 text-center">{{item.module_dynamic_attributes.length}}</td>
                <td style="font-size: 17pt;" class="col-2 text-center">{{item.module_permissions.length}}</td>
                <td class="col-2 text-center">
                  <b-btn variant="outline-warning" @click.prevent="openModuleEditModal(item)"><i class="fa fa-pencil"></i></b-btn>
                  <b-btn variant="outline-danger" @click.prevent="openDeleteModal(item)"><i class="fa fa-trash"></i></b-btn>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
        <div v-else>
          <table class="table">
            <thead>
              <tr class="text-center">No modules found</tr>
            </thead>
          </table>
        </div>

        <!-- add & edit  module modal  -->
        <b-modal id="addModuleModal" hide-footer hide-header no-close-on-backdrop>

          <div class="p-3 text-center">
            <b-form-input class="m-1" placeholder="Name" v-model="name"></b-form-input>
            <div class="py-3">
              <b-btn class="m-1" variant="outline-success" @click.prevent="saveModuleModal"><i class="fa fa-save"></i> {{addOrEdit == 'add'? 'Save' : 'Update'}}</b-btn>
              <b-btn v-if="addOrEdit == 'add'" class="m-1" @click.prevent="saveModuleAndAddNewModal" variant="outline-primary"><i class="fa fa-save"></i> <i class="fa fa-plus"></i> Save & add another</b-btn>
              <b-btn variant="outline-danger" @click.prevent="closeAddModuleModal"><i class="fa fa-close"></i> Close</b-btn>
            </div>
          </div>

        </b-modal>

        <!-- add fields in module modal  -->
        <b-modal id="addFieldInModule" no-close-on-backdrop hide-footer title="Add Field For Module">
          <b-form-select class="mb-2" v-model="addFieldsModule" @input="fetchFields" :options="selectModuleList"></b-form-select>
          <div v-if="selectFieldList.length > 0">
            <b-form-select class="mb-2" v-model="addFields" @input="fetchFields"  :options="selectFieldList"></b-form-select>
          </div>
          <div class="py-3 text-center">
            <b-btn class="m-1" variant="outline-primary" @click.prevent="addFieldInModuleSave"><i class="fa fa-save"></i> Save</b-btn>
            <b-btn class="m-1" variant="outline-danger" @click.prevent="closeAddFieldInModule"><i class="fa fa-close"></i> Close</b-btn>
          </div>
        </b-modal>

        <!-- delete modal  -->
        <b-modal id="deleteModuleModal" hide-footer hide-header no-close-on-backdrop>
          <h1 style="font-size: 16px;" class="p-3 text-center">Do you really want to delete the module?</h1>
          <div class="py-3 text-center">
            <b-btn class="m-1" variant="outline-danger" @click.prevent="deleteModule"><i class="fa fa-check"></i> Yes</b-btn>
            <b-btn class="m-1" variant="outline-primary" @click.prevent="dontDeleteModule"><i class="fa fa-times"></i> No</b-btn>
          </div>
        </b-modal>



        <b-modal id="checkUsersList" hide-footer title="List of users">
          <h3 class="text-center">{{moduleUsersList.name}}</h3>
          <div v-if="moduleUsersList != {}">
            <table class="table table-bordered">
              <thead class="thead-dark">
                <tr>
                  <th>Name</th>
                  <th class="text-center">Permission</th>
                  <!-- <th>Action</th> -->
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, index) in moduleUsersList.module_permissions" :key="index">
                  <td style="width: 70%; text-transform: capitalize;">{{item.user.name}}</td>
                  <td class="text-center" style="width: 30%;">{{item.permission}}</td>
                  <!-- <td>
                    <b-btn variant="outline-warning"><i class="fa fa-pencil"></i></b-btn>
                    <b-btn variant="outline-danger"><i class="fa fa-trash"></i></b-btn>
                  </td> -->
                </tr>
              </tbody>
            </table>
          </div>
          <div v-else class="text-center">
            No records found.
          </div>
        </b-modal>
    </div>
</template>


<script>
export default {
    name: 'AdminModuleTab',
    // props: ['module_list'],
    data() {
      return {
        moduleUsersList: {},
        module_list: [],
        name: null,
        selectModuleList: [],
        selectFieldList: [],
        addFieldsModule: null,
        addFields: null,
        addOrEdit: null,
        moduleToBeDeleted: null,
        permissions: [
          {
            value: null,
            text: 'Select an option.',
            disabled: true
          },{
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
      }
    },
    mounted() {
      this.fetchModules()
    },
    methods: {
      async addFieldInModuleSave() {
        try {
          if (this.addFieldsModule && this.addFields) {
            const resp = await this.$axios.post('/dynamic-attribute/add-to-module', {
              module_id: this.addFieldsModule,
              attribute_id: this.addFields
            })
            this.fetchModules()
            if (resp.data.data.added) {
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
          } else {
            this.$bvToast.toast( "Please fill all the fields." , {
              variant: 'danger',
              solid: true
            })
          }
          this.closeAddFieldInModule()
        } catch (error) {
          console.log(error);
        }
      },
      closeAddFieldInModule() {
        this.addFieldsModule = null
        this.addFields = null
        this.$bvModal.hide('addFieldInModule')
      },
      async fetchFields() {
        try {
          if (this.addFieldsModule) {
            this.selectFieldList = []
            const resp = await this.$axios.get(`/dynamic-attribute/fetch-all-fields?module_id=${this.addFieldsModule}`)
            this.selectFieldList = resp.data.data.responseData
          }
        } catch (error) {
          console.log(error);
        }
      },
      openAddFieldsInModuleModal() {
        this.selectModuleList = []
        this.module_list.forEach(e => {
          this.selectModuleList.push({
            value: e.id,
            text: e.name
          })
        })
        this.selectModuleList.unshift({
            value: null,
            text: 'Select a module.',
            disabled: true
          })
        this.$bvModal.show("addFieldInModule")
      },
      openAddModuleModal() {
        this.addOrEdit = 'add'
        this.$bvModal.show('addModuleModal')
      },
      async OpenUserListModal(item) {
        try {
          const resp = await this.$axios.get(`/module/fetch/${item.id}`)
          this.moduleUsersList = resp.data.data.module
          // console.log(this.moduleUsersList);
          this.$bvModal.show('checkUsersList')
        } catch (error) {
          console.log(error);
        }
      },
      openModuleEditModal(item) {
        this.name = item.name
        this.addOrEdit = 'edit '+item.id
        this.$bvModal.show('addModuleModal')
      },
      async saveModuleAndAddNewModal() {
        try {
          await this.saveModuleModal()
          this.openAddModuleModal()
        } catch (error) {
          console.log(error);
        }
      },
      closeAddModuleModal() {
        this.name = null
        this.$bvModal.hide('addModuleModal')
      },
      async saveModuleModal() {
        try {
          if (this.addOrEdit == 'add') {
            const payload = {
              name: this.name
            }
            const resp = await this.$axios.post('/module/add', payload)
            if (resp.data.data.created) {
              this.$bvToast.toast( resp.data.message , {
                variant: 'success',
                solid: true
              })
              this.name = null
              this.addOrEdit = null
              this.fetchModules()
              this.closeAddModuleModal()
            } else {
              this.$bvToast.toast( resp.data.message , {
                variant: 'danger',
                solid: true
              })
              this.name = null
              this.addOrEdit = null
              this.fetchModules()
              this.closeAddModuleModal()
            }
          } else {
            const payload = {
              id: Number(this.addOrEdit.replace('edit ', '')),
              name: this.name
            }
            const resp = await this.$axios.post('/module/edit', payload)
            if (resp.data.data.updated) {
              this.$bvToast.toast( resp.data.message , {
                variant: 'success',
                solid: true
              })
              this.name = null
              this.addOrEdit = null
              this.fetchModules()
              this.$bvModal.hide('addModuleModal')
            } else {
              this.$bvToast.toast( resp.data.message , {
                variant: 'danger',
                solid: true
              })
              this.name = null
              this.addOrEdit = null
              this.fetchModules()
              this.$bvModal.hide('addModuleModal')
            }

                        
          }
        } catch (error) {
          console.log(error);
        }
      },
      openDeleteModal(item) {
        this.moduleToBeDeleted = item.id
        this.$bvModal.show('deleteModuleModal')
      },
      async dontDeleteModule() {
        this.moduleToBeDeleted = null
        this.$bvModal.hide('deleteModuleModal')
      },
      async deleteModule() {
        try {
          const resp = await this.$axios.post('/module/delete', {id: this.moduleToBeDeleted})
          this.$bvToast.toast( resp.data.message , {
            variant: 'danger',
            solid: true,
          })
          this.moduleToBeDeleted = null
          await this.fetchModules()
          this.$bvModal.hide('deleteModuleModal')
        } catch (error) {
          console.log(error);
        }
      },
      async fetchModules() {
        try {
          const resp = await this.$axios.get('module/fetch')
          this.module_list = resp.data.data.data.map(e => {
            return {
              ...e
            }
          })
        } catch (error) {
          console.log(error);
        }
      }
    }
}
</script>