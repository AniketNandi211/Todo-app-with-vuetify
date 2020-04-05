<template>
  <v-container fluid class="pa-1">
    <v-list three-line subheader elevation="0" class="grey lighten-5">
      <v-subheader class="font-weight-black ml-1">Manage your activities</v-subheader>
      <!-- comment to be inserted -->
      <v-slide-x-transition group>
        <v-hover v-slot:default="{hover}" v-for="(item, index) in items" :key="index">
          <v-list-item :ripple="true">
            <v-list-item-icon>
              <v-avatar class="success">
                <v-icon
                  class="white lighten-4"
                  :class="`${item.icon.color}--text`"
                >{{item.icon.font}}</v-icon>
              </v-avatar>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title class="mt-n1">{{item.title}}</v-list-item-title>
              <v-list-item-subtitle class="mt-0 ml-1">{{item.desc}}</v-list-item-subtitle>
            </v-list-item-content>
            <v-list-item-action>
              <v-row align="center">
                <v-slide-y-transition>
                  <v-btn icon v-show="hover" color="success">
                    <v-icon v-show="hover">mdi-checkbox-marked-circle</v-icon>
                  </v-btn>
                </v-slide-y-transition>

                <v-slide-x-reverse-transition>
                  <v-btn v-show="hover" icon color="error" :loading="false">
                    <v-icon>mdi-delete</v-icon>
                  </v-btn>
                </v-slide-x-reverse-transition>
              </v-row>
            </v-list-item-action>
          </v-list-item>
        </v-hover>
      </v-slide-x-transition>
    </v-list>
  </v-container>
</template>
<script>
export default {
  props: ["items"],
  data: () => ({
    //
  }),
  methods: {
    //
  }
};
</script>