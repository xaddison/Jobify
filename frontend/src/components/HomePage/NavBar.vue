<template>
  <div v-if="checkLocalStorage()">
    <v-app-bar
      app
      dark 
      fixed
      short
      elevation="3"
      src="https://cdn.vuetifyjs.com/images/backgrounds/vbanner.jpg">
      <v-toolbar-title large style="cursor: pointer" @click="$router.push('/home')" >Jobify</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-menu offset-y>
        <template v-slot:activator="{ on, attrs }">
          <v-btn 
            v-bind="attrs"
            v-on="on"
            icon id="user-img">
          <v-avatar>
            <img
              src="../../assets/jobify2.jpg"
              alt="John">
          </v-avatar>
          </v-btn>           
        </template>
        <v-list width="300">
          <v-list-item-group>
            <v-list-item row wrap align-center :to="viewPath">
              <v-flex md3>
                <v-avatar size="35">
                <img
                  width="35" height="35"
                  src="../../assets/jobify2.jpg"
                  alt="John">
                </v-avatar>
              </v-flex>
                <v-flex md9> 
                <span>View Profile</span>
              </v-flex>
            </v-list-item>
            
            <v-list-item row wrap align-center v-show="t" @click="$router.push('/jobs/' + currUserId)">
              <v-flex md3>
                <v-icon class="icons_menu">mdi-briefcase</v-icon>
              </v-flex>
              <v-flex md9>
                <span class="spans-menu">My Jobs</span>
              </v-flex>  
            </v-list-item>
            
            <v-list-item row wrap align-center :to="editPath">
              <v-flex md3>
                <v-icon class="icons_menu">mdi-wrench</v-icon>
              </v-flex>
              <v-flex md9>
                <span class="spans-menu">Edit Profile</span>
              </v-flex>  
            </v-list-item>
            <v-list-item row wrap align-center v-on:click="logout">
              <v-flex md3>
                <v-icon class="icons_menu">mdi-export</v-icon>
              </v-flex>
              <v-flex md9>  
                <span class="spans-menu">Logout</span>
              </v-flex>  
            </v-list-item>
          </v-list-item-group>
        </v-list>
      </v-menu>
    </v-app-bar>
  </div>
</template>
<script>
export default {
  data(){
    return{
      type: false,
      currUserId: localStorage.getItem('userID'),
      currUserType : localStorage.getItem('userType'),
      profile_img: localStorage.getItem('userImageUrl'),
      userType: localStorage.getItem('userType'),
      t: (localStorage.getItem('userType')=="recruiter")
    }
  },
  computed: {
    editPath() {
      if(this.currUserType === 'applicant') {
        return `/editapplicantprofile/${this.currUserId}`
      }
      else {
        return `/editrecruiterprofile/${this.currUserId}`
      }
    
    },
    viewPath() {
      if(this.currUserType === 'applicant') {
        return `/applicantprofile/${this.currUserId}`
      }
      else {
        return `/recruiterprofile/${this.currUserId}`
      }
    }
  },
  methods: {
    logout: function(){
        localStorage.removeItem("userToken");
        localStorage.removeItem("userID");
        localStorage.removeItem("userEmail");
        localStorage.removeItem("userType");
        localStorage.removeItem("userImageUrl");
        localStorage.removeItem("additionalData");
        localStorage.removeItem("onModel");
        localStorage.clear();
        this.$router.push({path: '/login'})
    },
    checkLocalStorage: function(){
      console.log("entering check local storage");
      console.log(localStorage.getItem('userToken'));
      if(localStorage.getItem('userToken') == null)
        return false;
      return true;  
    }
  },
};
</script>
<style>
a{
    text-decoration: none;
    color: black;
}
.spans-menu{
  color: black;
}

#user-img{
  margin: 5px;
}
</style>
