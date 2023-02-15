<template>
  <div class="container">
    <NavBar style="width: 100%;" />
    <b-tabs class="mt-1" content-class="mt-1">
      <b-tab :active="currentTab == tabs.modules" title="Modules" @click.prevent="setCurrentTab(1)">
        <adminModuleTab v-if="currentTab == tabs.modules"/> 
      </b-tab>
      <b-tab :active="currentTab == tabs.users" title="Users" @click.prevent="setCurrentTab(2)">
        <AdminUserTab v-if="currentTab == tabs.users" />
      </b-tab>
      <b-tab :active="currentTab == tabs.fields" title="Fields" @click.prevent="setCurrentTab(3)">
        <FieldsTab v-if="currentTab == tabs.fields" />
      </b-tab>
    </b-tabs>
  </div>
</template>

<script>
import NavBar from "../components/navBar.vue";
import AdminUserTab from "../components/adminUserTab.vue";
import adminModuleTab from '../components/adminModuleTab.vue';
import FieldsTab from '../components/fieldsTab'
export default {
  name: "IndexPage",
  middleware: "auth",
  components: {
    NavBar,
    AdminUserTab,
    adminModuleTab
  },
  data() {
    return {
      currentTab: 1,
      tabs: {
        modules: 1,
        users: 2,
        fields: 3
      },
      // permissions: [
      //   {
      //     value: null,
      //     text: 'Select an option.',
      //     disabled: true
      //   },{
      //     value: 'EDIT',
      //     text: 'EDIT'
      //   }, {
      //     value: 'VIEW',
      //     text: 'VIEW'
      //   }, {
      //     value: 'ALL',
      //     text: 'ALL'
      //   }
      // ],
      // users_list: [],
      // add_user: {},
      // module_list: []
    }
  },
  mounted() {
    if (this.$store.state?.auth?.user?.role == 'SUPER') {
      if (!this.$auth.loggedIn || !this.$store.state.auth.user.id){
        return this.$router.push('/login')
      }
    } else {
      if (!this.$auth.loggedIn || !this.$store.state.auth.user.id){
        return this.$router.push('/login')
      }
      return this.$router.push('/indexForUser')
    }
    // this.fetchUsers()
    // this.fetchModules()
  },
  methods: {
    setCurrentTab(num) {
      this.currentTab = num
    },
    // async fetchUsers() {
    //   try {
    //     const resp = await this.$axios.get('user/list')
    //     this.users_list = resp.data.data.user.map(e => {
    //       let elem =  {
    //         Name: e.name,
    //         Email: e.email
    //       }
    //       delete e.name
    //       delete e.email
    //       return {
    //         ...elem,
    //         ...e
    //       }
    //     })
    //   } catch (error) {
    //     console.log(error);
    //   }
    // },
    // async fetchModules() {
    //   try {
    //     const resp = await this.$axios.get('module/fetch')
    //     this.module_list = resp.data.data.data.map(e => {
    //       return {
    //         ...e
    //       }
    //     })
    //   } catch (error) {
    //     console.log(error);
    //   }
    // }
  }
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
</style>
