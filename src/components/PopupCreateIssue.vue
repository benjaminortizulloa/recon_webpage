<template>
  <v-card
    color="white"
    style="overflow-y: scroll;"
    max-height="600px"
    shaped
    rounded
    raised
    elevation-24
    class="rounded-xl pa-5"
  >
    <v-card-title>Create a task</v-card-title>
    <v-card-text>
      <v-row>
        <v-col cols="6">
          <v-row>
            <v-col>
              <p>
                <b>Use text content to describe the task. Short and clear descriptions are most likely to generate interest.</b>
              </p>
              <v-text-field
                label="Task Title"
                v-model="formInfo.title"
                hint="A concise, informative title"
              ></v-text-field>
              <v-textarea
                label="Task Description"
                v-model="formInfo.description"
                rows="2"
                hint="A clear overview of the task which provides context, identifies key problems and specifies the work to be done, with itemized by deliverables"
              ></v-textarea>
              <v-text-field
                label="Impact"
                v-model="formInfo.impact"
                hint="A quick explanation of how addressing this task will help improving COVID-19 analytics"
              ></v-text-field>
              <v-text-field
                label="Timeline"
                v-model="formInfo.timeline"
                hint="A proposed timeline for the work."
              ></v-text-field>
            </v-col>
          </v-row>
        </v-col>
        <v-col cols="6">
          <v-row>
            <v-col>
              <p>
                <b>Use available labels to reflect the nature of the task. In particular, for rating the complexity and the priority of the task.</b>
              </p>
              <v-select
                label="Task Complexity"
                v-model="formInfo.complexity"
                :items="complexityTypes"
              ></v-select>
              <v-select label="Task priority" v-model="formInfo.priority" :items="priorityTypes"></v-select>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <p>
                <b>Assign task to specific github repo</b>
              </p>
              <v-select
                width="100%"
                v-model="formInfo.repo"
                :items="repoLabels"
                label="Assigned Repo"
              ></v-select>
              <!-- <v-select
                width="100%"
                v-model="formInfo.assignees"
                :items="collaborators"
                label="Assignee to Help"
              ></v-select>-->
            </v-col>
          </v-row>
        </v-col>
      </v-row>
    </v-card-text>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn @click="submitForm">Submit</v-btn>
      <v-btn @click="close">Close</v-btn>
      <v-spacer></v-spacer>
    </v-card-actions>
  </v-card>
</template>
<script>
import axios from "axios";
export default {
  name: "PopupCreateIssue",
  props: ["popup", "token", "user", "repos"],
  data() {
    return {
      priorityTypes: [
        { text: "Low - Useful for COVID19 analytics", value: "Priority_Low" },
        {
          text: "Medium - Essential for COVID19 analytics",
          value: "Priority_Medium"
        },
        { text: "High - Urgent for COVID19 analytics", value: "Priority_High" }
      ],
      complexityTypes: [
        {
          text: "Low - Can be completed by 1 person in a few hours",
          value: "Complexity_Low"
        },
        {
          text: "Medium - Can be completed by 1 person in a few (<5) days",
          value: "Complexity_Medium"
        },
        {
          text:
            "High - Requires multiple people and/or specialists to complete",
          value: "Complexity_High"
        }
      ],
      formInfo: {
        title: "",
        description: "",
        impact: "",
        timeline: "",
        priority: "",
        complexity: "",
        repo: "Do not know",
        assignees: "Do not know"
      },
      repoLabels: ["Do not know"],
      collaborators: ["Do not know"]
    };
  },
  methods: {
    close() {
      this.$emit("updatePopup", { type: "" });
    },
    submitForm() {
      console.log("inside submit form");
      let self = this;
      if (this.formInfo.assignees == 0) {
        this.formInfo.assignees = "__NONE__";
      }

      let qry = `${process.env.VUE_APP_API}/submitIssue?title=${
        this.formInfo.title
      }&author=${this.user}&body=${this.formInfo.description}&impact=${
        this.formInfo.impact
      }&timeline=${this.formInfo.timeline}&priority=${
        this.formInfo.priority
      }&complexity=${this.formInfo.complexity}&repo=${
        this.formInfo.repo
      }&assignees=${this.formInfo.assignees}`;

      console.log(qry);
      axios
        .post(qry)
        .then(function(res) {
          console.log("success", res);
          self.formInfo.title = "";
          self.formInfo.description = "";
          self.formInfo.impact = "";
          self.formInfo.timeline = "";
          self.formInfo.priority = "";
          self.formInfo.complexity = "";
          self.formInfo.repo = "Do not know";
          self.formInfo.assignees = "";
          alert(
            "Thank you for submitting your task. It is waiting for admin approval."
          );
          self.close();
        })
        .catch(function(err) {
          alert(err);
          console.log(err);
          self.close();
        });
    },
    getCollaborators() {
      let self = this;
      axios
        .get(
          "https://api.github.com/repos/benjaminortizulloa/ExploreGitAPI/collaborators",
          {
            headers: {
              Accept: "application/vnd.github.v3+json",
              Authorization: `token ${this.token}`
            }
          }
        )
        .then(function(res) {
          console.log("collaborators", res.data);
          self.collaborators = self.collaborators.concat(
            res.data.map(d => d.login)
          );
        });
    }
  },
  mounted() {
    console.log("inside popup", this.popup);
    this.repoLabels = this.repoLabels.concat(this.repos.map(d => d.name));
    this.getCollaborators();
  }
};
</script>