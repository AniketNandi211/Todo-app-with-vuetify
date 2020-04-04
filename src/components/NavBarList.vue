<template>
  <v-list class="ma-1">
    <!--  Heading  -->
    <v-list-item>
      <v-list-item-content>
        <v-list-item-title class="primary--text">{{title}}</v-list-item-title>
      </v-list-item-content>
    </v-list-item>
    <v-divider></v-divider>
    <!--  Main Menu builder template  -->
    <v-list-item-group v-model="linkItem" active-class="primary--text">
      <v-list dense shaped class="ma- 2">
        <v-list-item v-for="(item, index) in links" :key="index" :to="item.path">
          <v-list-item-icon>
            <v-icon>{{item.icon}}</v-icon>
          </v-list-item-icon>
          <v-list-item-content>
            <v-list-item-title>{{item.title}}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-list-item-group>
  </v-list>
</template>

<script>
export default {
  name: "NavBarList",
  props: {
    title: String
  },
  data: () => ({
    linkItem: 0,
    links: [
      { icon: "mdi-home", title: "Home", path: "/" },
      { icon: "mdi-information", title: "About", path: "/about" }
    ]
  }),
  methods: {}
};
</script>