<template>
  <div>
    <nav-bar-app />
    <v-container>
      <v-row no-gutters>
        <v-flex md3 lg1>
          <side-bar-app />
        </v-flex>
        <v-flex md9 lg11>
          <jobTitles-table-app />
        </v-flex>
      </v-row>
    </v-container>
  </div>
</template>
<script>
import SideBar from "../../components/Admin/sidebar.vue";
import NavBar from "../../components/Admin/navbar.vue";
import jobTitlesTable from "../../components/Admin/jobTitlesTables.vue";
export default {
  name: "Home",
  components: {
    "nav-bar-app": NavBar,
    "side-bar-app": SideBar,
    "jobTitles-table-app": jobTitlesTable,
  },
};
</script>
