<template>
  <div class="container">
    <div class="mt-3">
      <!-- <pre>{{modules}}</pre> -->
      <table class="table table-bordered">
        <thead class="thead-dark">
          <tr>
            <th>Module</th>
            <th class="text-center">Permission</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in modules" :key="index">
            <!-- <pre>{{item}}</pre> -->
            <td
              class="col-10"
              onMouseOver="this.style.color='#0091ff'"
              onmouseleave="this.style.color='#70e5ff'"
              style="
                color: #70e5ff;
                font-size: 17pt;
                cursor: pointer;
                font-weight: 900;
              "
              @click.prevent="openModuleModal(item)"
            >
              {{ item.name }}
            </td>
            <td class="col-2 text-center" style="font-size: 15pt">
              <i
                :class="
                  item.module_permissions[0].permission == 'VIEW'
                    ? 'fa fa-eye'
                    : item.module_permissions[0].permission == 'ALL'
                    ? 'fa fa-globe'
                    : 'fa fa-pencil'
                "
              ></i>
              {{ item.module_permissions[0].permission }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <b-modal
      id="moduleOperation"
      size="xl"
      hide-footer
      :title="selectedModule ? selectedModule.name : ''"
    >
      <!-- <pre>{{selectedModule}}</pre> -->
      <div v-if="selectedModule" class="col-lg-12 col-md-12 col-sm-12">
        <div v-if="selectedModule.module_permissions.length > 0">
          <i
            :class="
              selectedModule.module_permissions[0].permission == 'EDIT'
                ? 'fa fa-pencil ml-3'
                : selectedModule.module_permissions[0].permission == 'VIEW'
                ? 'fa fa-eye ml-3'
                : selectedModule.module_permissions[0].permission == 'ALL'
                ? 'fa fa-globe ml-3'
                : ''
            "
          >
            {{
              selectedModule.module_permissions[0].permission == "EDIT"
                ? "You have editing permission."
                : selectedModule.module_permissions[0].permission == "VIEW"
                ? "You have viewing permission."
                : selectedModule.module_permissions[0].permission == "ALL"
                ? "You have admin permissions."
                : ""
            }}
          </i>
          <b-btn
            :disabled="
              selectedModule.module_permissions[0].permission == 'VIEW'
                ? true
                : false
            "
            @click.prevent="
              openModuleEditModal({
                id: selectedModule.id,
                name: selectedModule.name,
              })
            "
            class="ml-3 float-right"
            variant="outline-primary"
            ><i class="fa fa-pencil"></i> Edit module name</b-btn
          >
          <div v-if="selectedModule.module_dynamic_attributes.length > 0">
            <div
              v-for="(item, index) in selectedModule.module_dynamic_attributes"
              :key="index"
            >
              <div
                v-if="item.dynamic_attribute"
                class="col-lg-12 col-md-12 col-sm-12"
              >
                <!-- <pre>{{item}}</pre> -->
                <div
                  v-if="item.dynamic_attribute.attribute_type == 'Text'"
                  style="width: 70%"
                  class="border p-3 rounded mt-1"
                >
                  <p class="mb-0">
                    {{ item.dynamic_attribute.attribute_name }}
                  </p>
                  <b-input-group class="mt-1">
                    <b-form-input
                      :disabled="
                        selectedModule.module_permissions[0].permission ==
                        'VIEW'
                          ? true
                          : false
                      "
                      :placeholder="
                        'Enter ' + item.dynamic_attribute.attribute_name
                      "
                      v-model="item.dynamic_attribute.value"
                    ></b-form-input>
                    <b-btn
                      :disabled="
                        selectedModule.module_permissions[0].permission ==
                        'VIEW'
                          ? true
                          : false
                      "
                      class="ml-3"
                      variant="outline-success"
                      @click.prevent="
                        saveFieldValue(
                          item.id,
                          item.dynamic_attribute.value,
                          'no'
                        )
                      "
                      ><i class="fa fa-save"></i
                    ></b-btn>
                    <b-btn
                      :disabled="
                        selectedModule.module_permissions[0].permission ==
                        'VIEW'
                          ? true
                          : false
                      "
                      class="ml-3"
                      variant="outline-primary"
                      @click.prevent="
                        openFieldEditModal(item.dynamic_attribute)
                      "
                      ><i class="fa fa-pencil"></i
                    ></b-btn>
                  </b-input-group>
                </div>
                <div
                  v-else-if="
                    item.dynamic_attribute.attribute_type == 'Select Box'
                  "
                  style="width: 70%"
                  class="border p-3 rounded mt-1"
                >
                  <p class="mb-0">
                    {{ item.dynamic_attribute.attribute_name }}
                  </p>
                  <b-input-group class="mt-1">
                    <b-form-select
                      :disabled="
                        selectedModule.module_permissions[0].permission ==
                        'VIEW'
                          ? true
                          : false
                      "
                      :options="item.dynamic_attribute.attribute_value"
                      v-model="item.dynamic_attribute.value"
                    ></b-form-select>
                    <b-btn
                      :disabled="
                        selectedModule.module_permissions[0].permission ==
                        'VIEW'
                          ? true
                          : false
                      "
                      class="ml-3"
                      variant="outline-success"
                      @click.prevent="
                        saveFieldValue(
                          item.id,
                          item.dynamic_attribute.value,
                          'no'
                        )
                      "
                      ><i class="fa fa-save"></i
                    ></b-btn>
                    <b-btn
                      :disabled="
                        selectedModule.module_permissions[0].permission ==
                        'VIEW'
                          ? true
                          : false
                      "
                      @click.prevent="
                        openFieldEditModal(item.dynamic_attribute)
                      "
                      class="ml-3"
                      variant="outline-primary"
                      ><i class="fa fa-pencil"></i
                    ></b-btn>
                  </b-input-group>
                </div>
                <div
                  v-else-if="
                    item.dynamic_attribute.attribute_type == 'Radio Button'
                  "
                  style="width: 70%"
                  class="border p-3 rounded mt-1"
                >
                  <p class="mb-0 mr-3">
                    {{ item.dynamic_attribute.attribute_name }}
                  </p>
                  <b-input-group class="mt-1">
                    <b-form-group>
                      <b-form-radio-group
                        :disabled="
                          selectedModule.module_permissions[0].permission ==
                          'VIEW'
                            ? true
                            : false
                        "
                        v-model="item.dynamic_attribute.value"
                        :options="item.dynamic_attribute.attribute_value"
                      ></b-form-radio-group>
                    </b-form-group>
                    <b-btn
                      :disabled="
                        selectedModule.module_permissions[0].permission ==
                        'VIEW'
                          ? true
                          : false
                      "
                      class="ml-3"
                      variant="outline-success"
                      @click.prevent="
                        saveFieldValue(
                          item.id,
                          item.dynamic_attribute.value,
                          'no'
                        )
                      "
                      ><i class="fa fa-save"></i
                    ></b-btn>
                    <b-btn
                      :disabled="
                        selectedModule.module_permissions[0].permission ==
                        'VIEW'
                          ? true
                          : false
                      "
                      @click.prevent="
                        openFieldEditModal(item.dynamic_attribute)
                      "
                      class="ml-3"
                      variant="outline-primary"
                      ><i class="fa fa-pencil"></i
                    ></b-btn>
                  </b-input-group>
                </div>
                <div
                  v-else-if="
                    item.dynamic_attribute.attribute_type == 'Check Box'
                  "
                  style="width: 70%"
                  class="border p-3 rounded mt-1"
                >
                  {{ item.dynamic_attribute.attribute_name }}
                  <b-input-group class="mt-1">
                    <b-form-checkbox
                      :disabled="
                        selectedModule.module_permissions[0].permission ==
                        'VIEW'
                          ? true
                          : false
                      "
                      v-model="item.dynamic_attribute.value"
                      class="mt-1"
                    ></b-form-checkbox>
                    <b-btn
                      :disabled="
                        selectedModule.module_permissions[0].permission ==
                        'VIEW'
                          ? true
                          : false
                      "
                      class="ml-2"
                      variant="outline-success"
                      @click.prevent="
                        saveFieldValue(
                          item.id,
                          item.dynamic_attribute.value,
                          'no'
                        )
                      "
                      ><i class="fa fa-save"></i
                    ></b-btn>
                    <b-btn
                      :disabled="
                        selectedModule.module_permissions[0].permission ==
                        'VIEW'
                          ? true
                          : false
                      "
                      @click.prevent="
                        openFieldEditModal(item.dynamic_attribute)
                      "
                      class="ml-3"
                      variant="outline-primary"
                      ><i class="fa fa-pencil"></i
                    ></b-btn>
                  </b-input-group>
                </div>
              </div>
            </div>
          </div>
          <div v-else>
            <table class="table">
              <thead>
                <tr class="text-center">
                  No fields found.
                </tr>
              </thead>
            </table>
          </div>
        </div>
      </div>
    </b-modal>
    <b-modal id="deleteFieldValue" size="xl" hide-footer hide-header>
      <div class="text-center p-3">
        <h5 class="text-center m-3">
          Do you really want to delete the field's value?
        </h5>
        <div class="text-center p-3">
          <b-button
            variant="outline-danger"
            @click.prevent="
              saveFieldValue(
                selectedFieldAttributeId,
                selectedFieldValue,
                'yes'
              )
            "
            >Yes</b-button
          >
          <b-button
            variant="outline-primary"
            @click.prevent="closeFieldUpdateModal"
            >No</b-button
          >
        </div>
      </div>
    </b-modal>
    <!-- edit  module modal  -->
    <b-modal id="addModuleModal" hide-footer hide-header no-close-on-backdrop>
      <div class="p-3 text-center">
        <b-form-input
          class="m-1"
          placeholder="Name"
          v-model="name"
        ></b-form-input>
        <div class="py-3">
          <b-btn
            class="m-1"
            variant="outline-success"
            @click.prevent="saveModuleModal"
            ><i class="fa fa-save"></i>
            {{ addOrEdit == "add" ? "Save" : "Update" }}</b-btn
          >
          <b-btn variant="outline-danger" @click.prevent="closeAddModuleModal"
            ><i class="fa fa-close"></i> Close</b-btn
          >
        </div>
      </div>
    </b-modal>
    <!-- edit field  -->
    <b-modal
      id="editFieldModal"
      :title="'Edit field'"
      hide-footer
      no-close-on-backdrop
    >
      <div>
        <b-form-input
          placeholder="Enter the task name"
          v-model="add_task.attribute_name"
        ></b-form-input>
        <b-form-select
          :options="task_types"
          v-model="add_task.attribute_type"
        ></b-form-select>
        <div
          v-if="
            add_task.attribute_type == 'Select Box' ||
            add_task.attribute_type == 'Radio Button'
          "
        >
          <div v-for="(e, i) in add_task_select_values" :key="i">
            <div class="selected-items ml-1">
              <div>
                {{ e }}
                <i
                  class="fa fa-trash"
                  id="delete-select"
                  @click.prevent="deleteElement(i)"
                  aria-hidden="true"
                ></i>
              </div>
            </div>
          </div>
          <b-form-input
            placeholder="Enter the values for select box"
            v-model="add_task.attribute_value"
            @keypress="checkEnter($event)"
          ></b-form-input>
        </div>
        <div class="text-center">
          <b-btn
            class="mt-3"
            variant="outline-success"
            @click.prevent="saveTask"
          >
            <i class="fa fa-check"></i
            >{{ add_task.id ? " Update" : " Save" }}</b-btn
          >
          <b-btn class="mt-3" @click="closeModal" variant="outline-danger">
            <i class="fa fa-close"></i> Close</b-btn
          >
        </div>
      </div>
    </b-modal>
  </div>
</template>


<script>
export default {
  data() {
    return {
      add_task: {
        id: null,
        attribute_type: null,
        attribute_name: null,
        attribute_value: null,
      },
      add_task_select_values: [],
      task_types: [
        {
          value: null,
          text: "Select a Type",
          disabled: true,
        },
        {
          value: "Text",
          text: "Text",
        },
        {
          value: "Radio Button",
          text: "Radio Button",
        },
        {
          value: "Check Box",
          text: "Check Box",
        },
        {
          value: "Select Box",
          text: "Select Box",
        },
      ],
      modules: {},
      selectedModule: null,
      selectedModuleId: null,
      selectedFieldValue: null,
      selectedFieldAttributeId: null,
      name: null,
      addOrEdit: null,
    };
  },
  mounted() {
    this.fetchModules();
  },
  methods: {
    async saveTask() {
      try {
        this.add_task.attribute_value =
          this.add_task.attribute_type == "Select Box" ||
          this.add_task.attribute_type == "Radio Button"
            ? this.add_task_select_values?.join(",")
            : null;
        if (!this.add_task.attribute_name) {
          this.$bvToast.toast("Enter attribute name.", {
            variant: "danger",
            solid: true,
          });
          return;
        }
        if (!this.add_task.attribute_type) {
          this.$bvToast.toast("Enter attribute type.", {
            variant: "danger",
            solid: true,
          });
          return;
        }
        if (
          this.add_task.attribute_type == "Select Box" &&
          !this.add_task.attribute_value
        ) {
          this.$bvToast.toast("Enter attribute value.", {
            variant: "danger",
            solid: true,
          });
          return;
        }
        const resp = await this.$axios.post(
          "dynamic-attribute/edit",
          this.add_task
        );
        if (resp.data.message.includes("success")) {
          this.$bvToast.toast("Dynamic attribute updated successfully.", {
            variant: "success",
            solid: true,
          });
        } else {
          this.$bvToast.toast(
            resp?.data?.message.replace(
              resp?.data?.message[0],
              resp?.data?.message[0].toUpperCase()
            ),
            {
              variant: "danger",
              solid: true,
            }
          );
        }
        this.$bvModal.hide("moduleOperation");
        this.closeModal();
        await this.fetchModules();
        // console.log(this.modules.filter(e => e.id == this.selectedModuleId));
        this.openModuleModal(
          this.modules.filter(e => e.id == this.selectedModuleId)[0]
        );
      } catch (error) {
        console.log(error);
      }
    },
    closeModal() {
      this.add_task = {
        id: null,
        attribute_name: null,
        attribute_type: null,
        attribute_value: null
      };
      this.add_task_select_values = [];
      this.$bvModal.hide("editFieldModal");
    },
    deleteElement(i) {
      this.add_task_select_values.splice(i, 1);
    },
    checkEnter(event) {
      if (event.charCode == 13) {
        this.add_task_select_values.push(this.add_task.attribute_value);
        this.add_task.attribute_value = null;
      }
    },
    openFieldEditModal(item) {
      this.add_task.id = item.id;
      this.add_task.attribute_name = item.attribute_name;
      this.add_task.attribute_type = item.attribute_type;
      this.add_task.attribute_value = null;
      this.add_task_select_values = item.attribute_value ? item.attribute_value.map(e =>{ if(e.value) return e.text }).filter(e => e) : []
      this.$bvModal.show("editFieldModal");
    },
    openModuleEditModal(item) {
      this.selectedModuleId = item.id;
      this.name = item.name;
      this.addOrEdit = "edit";
      this.$bvModal.show("addModuleModal");
    },
    closeAddModuleModal() {
      this.selectedModuleId = null;
      this.name = null;
      this.addOrEdit = null;
      this.$bvModal.hide("addModuleModal");
    },
    async saveModuleModal() {
      try {
        const payload = {
          id: Number(this.selectedModuleId),
          name: "" + this.name,
        };
        const resp = await this.$axios.post("/module/edit", payload);
        if (resp.data.data.updated) {
          this.$bvToast.toast(resp.data.message, {
            variant: "success",
            solid: true,
          });
          this.name = null;
          this.addOrEdit = null;
          this.$bvModal.hide("addModuleModal");
        } else {
          this.$bvToast.toast(resp.data.message, {
            variant: "danger",
            solid: true,
          });
          this.name = null;
          this.addOrEdit = null;
          this.$bvModal.hide("addModuleModal");
        }
        this.$bvModal.hide("moduleOperation");
        this.fetchModules();
        this.closeAddModuleModal();
      } catch (error) {
        console.log(error);
      }
    },
    valueSet(val) {
      console.log(val);
    },
    closeFieldUpdateModal() {
      this.selectedFieldValue = null;
      this.selectedFieldAttributeId = null;
      this.$bvModal.hide("deleteFieldValue");
    },
    async saveFieldValue(attribute_id, value, sure = "no") {
      try {
        this.selectedFieldValue = value == "" ? null : "" + value;
        this.selectedFieldAttributeId = attribute_id;
        if (!value && sure == "no") {
          this.$bvModal.show("deleteFieldValue");
          return;
        }
        const resp = await this.$axios.post("/field-value/add", {
          attribute_id: this.selectedFieldAttributeId,
          value: this.selectedFieldValue,
        });
        if (resp.data.data.operation) {
          this.$bvToast.toast(resp.data.message, {
            variant: "success",
            solid: true,
          });
        } else {
          this.$bvToast.toast(resp.data.message, {
            variant: "danger",
            solid: true,
          });
        }
        this.fetchModules();
        // console.log(this.modules.filter(e => e.id == this.selectedModuleId));
        this.openModuleModal(
          this.modules.filter((e) => e.id == this.selectedModuleId)[0]
        );
        this.closeFieldUpdateModal();
      } catch (error) {
        console.log(error);
      }
    },
    async openModuleModal(data) {
      this.selectedModuleId = data.id;
      await data.module_dynamic_attributes.forEach((e) => {
        if (e.dynamic_attribute.attribute_type == "Select Box") {
          e.dynamic_attribute.value = e.dynamic_attributes_value
            ? e.dynamic_attributes_value.value
            : null;
          if (typeof e.dynamic_attribute.attribute_value === "string") {
            e.dynamic_attribute.attribute_value =
              e.dynamic_attribute.attribute_value.split(",").map((e) => {
                return {
                  value: e,
                  text: e,
                };
              });
            e.dynamic_attribute.attribute_value.unshift({
              value: null,
              text: "Select an option.",
              disabled: true,
            });
          }
        }
        if (e.dynamic_attribute.attribute_type == "Radio Button") {
          e.dynamic_attribute.value = e.dynamic_attributes_value
            ? e.dynamic_attributes_value.value
            : null;
          if (typeof e.dynamic_attribute.attribute_value === "string") {
            e.dynamic_attribute.attribute_value =
              e.dynamic_attribute.attribute_value.split(",").map((e) => {
                return {
                  value: e,
                  text: e,
                };
              });
          }
        }
        if (e.dynamic_attribute.attribute_type == "Check Box") {
          e.dynamic_attribute.value = e.dynamic_attributes_value
            ? e.dynamic_attributes_value.value
            : null;
          if (typeof e.dynamic_attribute.attribute_value === "string") {
            e.dynamic_attribute.attribute_value = Boolean(
              e.dynamic_attribute.attribute_value
            );
          }
        }
        e.dynamic_attribute.value = e.dynamic_attributes_value
          ? e.dynamic_attributes_value.value
          : null;
      });
      this.selectedModule = data;
      this.$bvModal.show("moduleOperation");
    },
    async fetchModules() {
      try {
        const resp = await this.$axios.get(
          `/module/fetch-for-user/${this.$store.state.auth.user.id}`
        );
        this.modules = resp.data.data.module;
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>

<style scoped>
.container {
  padding: 0%;
}
.module-tabs-container {
  height: 330px;
  font-size: 12px;
  max-height: 330px;
  /* border: 1px solid black; */
}
.user-tabs-container {
  margin-top: 5px;
  height: 330px;
  max-height: 330px;
  font-size: 12px;
  /* border: 1px solid black; */
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
</style>