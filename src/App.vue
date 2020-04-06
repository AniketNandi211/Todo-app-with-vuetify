<template>
  <v-app>
    <v-app-bar app dark dense extended color="primary">
      <!-- using template with activator slot to activate tooltip on hover -->
      <v-tooltip bottom color="primary--text white" transition="slide-x-reverse-transition">
        <template v-slot:activator="{ on }">
          <v-app-bar-nav-icon v-on="on" @click="drawer = !drawer">
            <v-icon medium>mdi-format-list-bulleted</v-icon>
          </v-app-bar-nav-icon>
        </template>
        <span>Click to open menu</span>
      </v-tooltip>
      <v-toolbar-title>
        <span class="font-weight-bold">Activity Tracker</span>
        <sub class="caption">
          V-0.6
          Beta test
        </sub>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-chip small color="grey lighten-4">
        <v-btn icon color="green">
          <v-icon>mdi-checkbox-marked-circle-outline</v-icon>
        </v-btn>
        <v-btn icon color="error">
          <v-icon>mdi-cancel</v-icon>
        </v-btn>
      </v-chip>
      <!-- appbar-bottom-left-docked fab button-->
      <template v-slot:extension>
        <v-fab-transition>
          <v-btn fab bottom absolute v-show="scrollAmount > 150" color="white primary--text">
            <v-icon>mdi-plus</v-icon>
          </v-btn>
        </v-fab-transition>
      </template>
    </v-app-bar>
    <v-navigation-drawer app v-model="drawer">
      <NavBarList title="Overview" />
    </v-navigation-drawer>

    <v-content>
      <!-- Listening to scrollChange event from Home component to toggle add button -->
      <router-view @scrollChange="getValue"></router-view>
    </v-content>
  </v-app>
</template> 

<script>
import NavBarList from "@/components/NavBarList.vue";

export default {
  name: "App",

  components: { NavBarList },

  data: () => ({
    scrollAmount: 0,
    drawer: false
  }),
  methods: {
    getValue(val) {
      this.scrollAmount = val;
    }
  }
};
</script>
<style>
::-webkit-scrollbar {
  width: 8px;
}
::-webkit-scrollbar-track {
  background: grey;
}

::-webkit-scrollbar-thumb {
  background: rgb(47, 109, 190);
  border-radius: 15px;
}
</style>