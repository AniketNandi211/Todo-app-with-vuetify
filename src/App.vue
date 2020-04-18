<template>
  <v-app>
    <v-app-bar
      app
      shrink-on-scroll
      flat
      prominent
      dark
      dense
      extended
      color="primary"
      height="120"
      fade-img-on-scroll
      src="@/assets/work.jpg"
    >
      <template v-slot:img="{ props }">
        <v-img v-bind="props" gradient="to bottom right, rgba(0,0,255,.5), rgba(0,255,255,.6)"></v-img>
      </template>

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
          V-0.8
          Beta test
        </sub>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <!-- Just for visual, nothing functional -->
      <v-btn icon>
        <v-icon>mdi-checkbox-marked-circle-outline</v-icon>
      </v-btn>
      <v-btn icon>
        <v-icon>mdi-export</v-icon>
      </v-btn>
      <!-- appbar-bottom-left-docked fab button-->
      <template v-slot:extension>
        <v-fab-transition>
          <v-btn fab bottom absolute v-show="scrollAmount > 130" color="white primary--text">
            <v-icon>mdi-plus</v-icon>
          </v-btn>
        </v-fab-transition>
      </template>
    </v-app-bar>
    <v-navigation-drawer app v-model="drawer">
      <NavBarList title="Overview" />
    </v-navigation-drawer>

    <v-content class="grey lighten-3">
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
  width: 4px;
}
::-webkit-scrollbar-track {
  background: grey;
}

::-webkit-scrollbar-thumb {
  background: rgb(47, 109, 190);
  border-radius: 15px;
}
</style>