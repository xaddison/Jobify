<template>
    <div v-if="checkLocalStorage()">
        <h2 style="text-align: center; margin: 10px">Update Job</h2>
        <div class="text-center" v-if="loadingState">
            <v-progress-circular
                indeterminate
                color="primary"
            ></v-progress-circular>
        </div>
        <v-form class="form-post" ref="form" v-if="!loadingState">
            <v-text-field 
                style="margin: 20px"
                label="Job Title"
                outlined
                :rules="ValidationRulesText"
                :value="item.jobTitle"
                v-model="item.jobTitle"
                required
            ></v-text-field>
            <h3 style="margin: 10px; margin-left:15px">Job Details</h3>
            <v-select
                style="margin: 20px"
                :items="exprience_needed_list"
                label="Experience Needed"
                :value="item.experience"
                v-model="item.experience"
                outlined
                :rules="ValidationRulesText"
                required
            ></v-select>
            <v-select
                style="margin: 20px"
                :items="career_level_list"
                label="Career Level"
                outlined
                :value="item.careerLevel"
                v-model="item.careerLevel"
                :rules="ValidationRulesText"
                required
            ></v-select>
            <v-text-field 
                style="margin: 20px; width:30%"
                label="Salary (LE)"
                type="number"
                :value="item.salary"
                v-model="item.salary"
                outlined
                :rules="ValidationRulesNumbers"
                required
            ></v-text-field>
            <v-select
                style="margin: 20px"
                :items="job_category_list"
                label="Job Category"
                outlined
                :value="item.field"
                v-model="item.field"
                :rules="ValidationRulesText"
                required
            ></v-select>
            <v-select
                style="margin: 20px"
                v-model="item.skills"
                :items="skills_list"
                label="Skills"
                hint="You Can Select multiple skills"
                multiple
                chips
                outlined
            ></v-select>
            <v-textarea
                style="margin: 20px; margin-top:30px"
                outlined
                label="Job Description"
                :value="item.jobDescription"
                v-model="item.jobDescription"
                :rules="ValidationRulesText"
                required
            ></v-textarea>     
            <div style="margin:40px; text-align:center;">
                <v-btn
                    color="primary"
                    large
                    width = "120"
                    :disabled="addedSuccessfully"
                    @click="submitForm">
                    Update
                </v-btn>
                <v-alert v-if="show_success" style="margin-top: 20px"
                        type="success"
                >Job is updated successfully</v-alert>
            </div>
        </v-form>  
    </div>
</template>

<script>

import {exprience_needed_list, career_level_list, job_category_list} from "../../const.js";
export default {
    props:{
        job_id: String,
        init_skills: Array
    },
    data () {
        return {
            exprience_needed_list:[],
            career_level_list:[],
            job_category_list:[],
            skills_list:[],
            ValidationRulesText: [
                v => !!v || 'This field is required'
            ],
            ValidationRulesNumbers: [
                v => v>0 || 'This field is required to be positive'
            ],
            ValidationRulesSelect: [
                v => v.length>0 || 'This field is required'
            ],
            loadingState: true,
            errorMessage: "",
            response: {},
            items: [],
            selected_data: {},
            create_response: {},
            addedSuccessfully: false,
            show_success: false,
            item: {}
        }    
    },
   async mounted() {
     this.exprience_needed_list = exprience_needed_list,
     this.career_level_list = career_level_list,
     this.job_category_list = job_category_list,
     console.log("on mounted function in post job page")
      this.loadingState = true;
      this.errorMessage = ""
      try {
          this.response = await this.$store.dispatch("getAllSkills", {
          userToken : localStorage.getItem('userToken'),
          limit : 100,
          offset : 0
        })
        this.items = this.response.items;
        console.log(this.response);
        this.skills_list = this.items.map(value => value.name);

            console.log("on mounted function for job fetch")
            this.loadingState = true;
            this.errorMessage = ""
            try {
                this.response = await this.$store.dispatch("getJobDetails", {
                userToken : localStorage.getItem('userToken'),
                id : this.job_id
                })
                this.item = this.response;
                console.log("fetched job successfully");
                console.log(this.response);
                this.loadingState = false;
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
       async submitForm(){
            if(this.$refs.form.validate()){
                console.log(this.item);
                console.log("valid input, submit function");
                this.addedSuccessfully = true;
                this.errorMessage = "";
                try {
                    this.create_response = await this.$store.dispatch("editJob", {
                    userToken : localStorage.getItem('userToken'),
                    form_input: this.item
                    });
                    console.log(this.create_response);
                    this.show_success=true;
                    await new Promise(resolve => setTimeout(resolve, 1000));
                    this.$router.push({path: '/home'});
                } 
                catch (error) {
                    console.log("an error occured")
                    if(error.status === "fail") {
                    this.errorMessage = error.msg
                    }
                    else {
                    this.errorMessage = "Please try again later !"
                    }
                }
            } 
            else
                alert("invalid input");

        },
        checkLocalStorage: function(){
            console.log("entering check local storage");
            console.log(localStorage.getItem('userToken'));
            if(localStorage.getItem('userToken') == null)
                return false;
            return true;  
        }  
    }
};
</script>

<style>
.form-post{
    margin: 10px; 
    margin-left: auto;
    margin-right: auto;
    max-width: 70%;
}

</style>
