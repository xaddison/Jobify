<template>
    <v-container v-if="checkLocalStorage()">
        <div class="text-center" v-if="loadingState">
        <v-progress-circular
            indeterminate
            color="primary"
        ></v-progress-circular>
        </div>
        <v-row no-gutters v-if="!loadingState">
            <v-flex md8>
                <v-card
                    v-for="item in items"
                    v-bind:key="item"
                    v-show="checkVisibilty(item.recruiter._id)"
                    class="mx-auto job-card"
                    elevation="10"
                    outlined
                    hover>
                    <v-container wrap>
                        <v-row no-gutters >
                            <v-flex md10>
                                <v-card-title>{{item.jobTitle}}</v-card-title>
                                <v-card-subtitle>{{item.recruiter.additionalData.name}}</v-card-subtitle>
                            </v-flex>
                            <v-spacer></v-spacer>
                            <v-flex md1>
                                <v-btn 
                                v-bind="attrs"
                                v-on="on"
                                icon
                                id = "company-image">
                                <v-avatar>
                                    <img
                                    v-bind:src="item.recruiter.imageUrl" 
                                    alt="John">
                                </v-avatar>
                                </v-btn>
                            </v-flex>
                        </v-row>
                    </v-container>
                    <v-card-text>
                        {{item.jobDescription}}
                    </v-card-text>    
                    <v-card-actions>
                        <v-btn
                            color="blue darken-2"
                            text
                            @click="$router.push('/apply/' + item._id)">
                            Details
                        </v-btn>
                    </v-card-actions>
                </v-card>                
            </v-flex>
            <v-spacer></v-spacer>
            <v-flex md3>
                <v-card
                    class="mx-auto"
                    elevation="2"
                    max-width="350"
                    outlined>
                    <v-card-title>Improve Your Profile</v-card-title>
                    <v-card-subtitle v-show="type">So that the employers can be attracted to you</v-card-subtitle>
                    <v-card-subtitle v-show="!type">So that the employees can be attracted to your company</v-card-subtitle>
                    <v-card-actions class="pt-0">
                    <v-btn
                        text
                        color="light-blue darken-4"
                        :to="{
                            path: editPath,
                            query: { redirect: this.$route.query.redirect },
                        }"
                        @click="reveal = false">
                        Update Here
                    </v-btn>
                    </v-card-actions>
                </v-card>
            </v-flex>
        </v-row>
    </v-container>
</template>
<script>
export default {
    data () {
        return {
            type: true,
            currUserId: localStorage.getItem('userID'),
            loadingState: true,
            errorMessage: "",
            response: {},
            items: []
        }
    },
    async mounted(){
      console.log("on mounted function")
      this.loadingState = true;
      this.errorMessage = ""
      try {
          this.response = await this.$store.dispatch("getNewFeeds", {
          userToken : localStorage.getItem('userToken'),
          limit : 10,
          offset : 0
        })
        this.items = this.response.items;
        this.items = this.items.reverse();
        this.loadingState = false;
        console.log(this.response);
      } 
      catch (error) {
        console.log("an error occured")
        this.loadingState = false
        if(error.status === "fail") {
          this.errorMessage = error.msg
        }
        else {
          this.errorMessage = "Please try again later !"
        }
        console.log(error);
      }
    },
    methods: {
        checkVisibilty: function(itemID){
            if(itemID==this.currUserId)
                return false;
            return true;    
        },
        checkLocalStorage: function(){
            console.log("entering check local storage");
            console.log(localStorage.getItem('userToken'));
            if(localStorage.getItem('userToken') == null)
                return false;
            return true;  
        }
    },
    computed: {
        editPath() {
            if(localStorage.getItem('userType') === 'applicant') {
                return `editapplicantprofile/${this.currUserId}`
            }
            else {
                return `editrecruiterprofile/${this.currUserId}`
            }
        }
    }
};
</script>

<style>

#company-image{
    margin-top: 10px;
}
.job-card{
    margin: 10px;
}

</style>
