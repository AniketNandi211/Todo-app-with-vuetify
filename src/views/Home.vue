<template>
  <div class="home">
    <v-row class="mx-1">
      <v-col cols="12">
        <v-card elevation="2" tile class="pa-1">
          <v-card-title class="mt-1 primary--text" style="word-break: normal">
            <!-- word-break for applying word wrap -->
            Hey {{username}}, what are you up to today?
          </v-card-title>
          <v-card-subtitle class="mt-1 ml-2 primary--text">
            <span class="success--text">Don't forget to water the plants</span>
          </v-card-subtitle>
          <v-card-text class="text-right mt-n4">
            <v-progress-circular
              class="mr-6 success--text"
              size="70"
              :value="progression"
              rotate="-180"
            >
              <span class="font-weight-bold">{{completedTasks}} of {{taskCount}}</span>
            </v-progress-circular>
            <br />
            <!-- <div class="mt-1 mr-8 primary--text">status</div>  -->
          </v-card-text>
          <v-card-text class="mt-n8">
            <div class="font-weight-bold ml-2 mb-4 primary--text">Great, You are almost there</div>
            <v-chip :ripple="true" outlined color="success">
              <span class="ml-1">{{completedTasks}} completed</span>
              <v-icon small class="ml-2">mdi-checkbox-marked-circle-outline</v-icon>
            </v-chip>
            <v-chip :ripple="true" outlined class="mx-1 my-1" color="warning">
              <span class="ml-1">{{pendingTasks}} Pending</span>
              <v-icon small class="ml-1">mdi-timelapse</v-icon>
            </v-chip>
            <v-chip :ripple="true" outlined class="mx-0" color="error">
              <v-icon small class="mr-1">mdi-cancel</v-icon>
              <span class="ml-1">{{abortedTasks}} Aborted</span>
            </v-chip>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <!-- vvv -    bottom FAB button 
    this parent container detects scroll event to handle the FAB buttons
    (App bar docked button as well via emiting an event) 
    -->
    <v-container fluid v-scroll="fabScroll">
      <v-slide-y-reverse-transition>
        <v-btn fab dark bottom v-show="scrollAmount < 150" fixed right color="primary">
          <v-icon>mdi-plus</v-icon>
        </v-btn>
      </v-slide-y-reverse-transition>
      <!-- Component for display activities -->
      <ListView :items="listData" @taskStateChanged="completeTask" />
    </v-container>
  </div>
</template>

<script>
import ListView from "@/components/ListView.vue";
import axios from "axios";

export default {
  name: "Home",
  components: { ListView },
  data: () => ({
    scrollAmount: window.scrollY,
    username: "LoneStar",
    completeCount: 0,
    progressColor: "success",
    listData: []
  }),
  // Created Life cycle hook
  created: async function() {
    let todos = [];
    try {
      const res = await axios.get("http://localhost:3000/pending");
      todos = res.data;
      todos.map(({ id, title, desc, done, icon, time, date }) => {
        this.listData.push({
          id: id,
          title: title,
          desc: desc,
          done: done,
          icon: icon,
          time: time,
          date: date
        });
      });
    } catch (err) {
      console.log(err);
    }
  },
  methods: {
    fabScroll() {
      this.scrollAmount = window.scrollY;
      this.$emit("scrollChange", this.scrollAmount);
    },
    completeTask(/* completedTaskId */) {
      this.completeCount++;
    }
  },
  computed: {
    taskCount() {
      return this.listData.length;
    },
    pendingTasks() {
      return this.listData.length - this.completeCount;
    },
    completedTasks() {
      return this.completeCount;
    },
    progression() {
      return Math.floor((this.completeCount / this.listData.length) * 100);
    },
    abortedTasks() {
      return 0; // this.aborted.length
    }
  }
};
</script>

<style scoped>
</style>
