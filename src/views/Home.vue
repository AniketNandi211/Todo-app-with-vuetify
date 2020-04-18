<template>
  <div class="home">
    <v-row class="mx-0">
      <v-col cols="12">
        <v-card elevation="2" class="pa-1" v-ripple="{class : 'cyan--text'}">
          <v-card-title class="mt-1 primary--text" style="word-break: normal">
            <!-- word-break for applying word wrap -->
            Hey {{username}}, what are you up to today?
          </v-card-title>
          <v-card-subtitle class="mt-1 ml-2">
            <v-icon color="cyan" x-large>mdi-weather-night</v-icon>
            <span class="primary--text">Night sky, 28&deg;C</span>
          </v-card-subtitle>
          <v-card-text class="text-right mt-n4">
            <v-progress-circular
              class="mr-6"
              :class="progressionStyle"
              size="70"
              :value="progression"
              rotate="-180"
            >
              <span class="font-weight-bold" style="transition : all 200ms">
                <span :class="progressionStyle">{{completedTasks}}</span>
                of {{taskCount}}
              </span>
            </v-progress-circular>
            <br />
            <!-- <div class="mt-1 mr-8 primary--text">status</div>  -->
          </v-card-text>
          <v-card-text class="mt-n8">
            <div
              class="font-weight-bold ml-2 mb-4 primary--text"
            >Perfect weather for a small walk around</div>
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
        <v-btn fab dark bottom v-show="scrollAmount < 130" fixed right color="primary">
          <v-icon>mdi-plus</v-icon>
        </v-btn>
      </v-slide-y-reverse-transition>
      <!-- Component for display activities -->
      <ListView @dataLoaded="updateStatus" @taskStateChanged="taskDone" @itemDeleted="taskAborted" />
    </v-container>
  </div>
</template>

<script>
import ListView from "@/components/ListView.vue";

export default {
  name: "Home",
  components: { ListView },
  data: () => ({
    scrollAmount: window.scrollY,
    username: "Aniket",
    completeCount: 0,
    pendingCount: 0,
    totalCount: 0,
    abortedCount: 0,
    progressPerc: 0
  }),
  methods: {
    fabScroll() {
      this.scrollAmount = window.scrollY;
      this.$emit("scrollChange", this.scrollAmount);
    },
    updateStatus({ complete, total, abort }) {
      this.completeCount = complete;
      this.totalCount = total;
      this.abortedCount = abort;
    },
    taskDone(/* task_id */) {
      this.completeCount++;
    },
    taskAborted(/* task_id */) {
      this.abortedCount++;
      this.completeCount--;
    }
  },
  computed: {
    completedTasks() {
      return this.completeCount;
    },
    pendingTasks() {
      return this.totalCount - this.completeCount;
    },
    taskCount() {
      return this.totalCount;
    },
    progression() {
      return Math.floor((this.completeCount / this.totalCount) * 100);
    },
    abortedTasks() {
      return this.abortedCount;
    },
    progressionStyle() {
      let perc = Math.floor((this.completeCount / this.totalCount) * 100);
      if (perc === 0) {
        return { "error--text": true };
      } else if (perc > 0 && perc <= 20) {
        return { "orange--text": true };
      } else if (perc > 20 && perc <= 80) {
        return { "primary--text": true };
      } else {
        return { "success--text": true };
      }
    }
  }
};
</script>

<style scoped>
</style>
