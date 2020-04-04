<template>
  <v-container fluid class="pa-1">
    <v-list three-line subheader>
      <v-subheader class="font-weight-black ml-1">Manage your activities</v-subheader>
      <!-- comment to be inserted -->
      <v-slide-x-transition group>
        <v-hover v-slot:default="{hover}" v-for="(item, index) in items" :key="index">
          <v-list-item :ripple="false" @click="showInfo(index)">
            <v-list-item-icon>
              <v-avatar class="success">
                <v-icon class="red lighten-4 red--text">mdi-home</v-icon>
              </v-avatar>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title class="mt-n1">{{item.title}} - {{index}}</v-list-item-title>
              <v-list-item-subtitle class="mt-0 ml-1">#{{item.desc}}</v-list-item-subtitle>
            </v-list-item-content>
            <v-list-item-action>
              <v-row align="center">
                <v-slide-y-transition>
                  <v-checkbox v-show="hover" color="success success--text" v-model="item.done"></v-checkbox>
                </v-slide-y-transition>
                <v-slide-x-reverse-transition>
                  <v-btn v-show="hover" icon color="error">
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
    tasks: []
  }),
  methods: {
    showInfo(value) {
      alert(value);
    }
  }
};
</script>