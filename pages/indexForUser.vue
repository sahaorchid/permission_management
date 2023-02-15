<template>
    <div class="container">
        <NavBar style="width: 100%;" />
        <UserModuleOperations />
    </div>
</template>
<script>
import UserModuleOperations from '../components/userModuleOperations.vue'
export default {
    components: { UserModuleOperations },
    name: 'indexPageForUser',
    
}
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
