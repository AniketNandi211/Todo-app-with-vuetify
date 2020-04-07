<template>
  <v-container fluid class="pa-1">
    <v-list three-line subheader shaped elevation="0" class="grey lighten-4">
      <v-subheader class="font-weight-black ml-1">Manage your activities</v-subheader>
      <!-- comment to be inserted -->
      <v-slide-x-transition group>
        <v-hover v-slot:default="{hover}" v-for="(item, index) in items" :key="index">
          <v-list-item :ripple="true" class="my-1" :class="{'success lighten-5' : item.done}">
            <v-list-item-icon>
              <v-avatar class="success">
                <v-icon
                  class="white"
                  :color="`${item.icon.color}`"
                  :class="{'success lighten-4 success--text' : item.done}"
                >{{item.icon.font}}</v-icon>
              </v-avatar>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title class="mt-n1" :class="{'success--text' : item.done}">{{item.title}}</v-list-item-title>
              <v-list-item-subtitle
                class="mt-0 ml-1"
                :class="{'success--text' : item.done}"
              >{{item.desc}}</v-list-item-subtitle>
            </v-list-item-content>
            <v-list-item-action>
              <v-row align="center" class="mt-2">
                <v-slide-y-transition>
                  <v-btn
                    icon
                    color="success"
                    :loading="item.done"
                    v-show="hover && !item.done"
                    @click="completeItem(item.id, index)"
                  >
                    <v-icon v-show="hover">mdi-checkbox-marked-circle</v-icon>
                  </v-btn>
                </v-slide-y-transition>

                <v-slide-x-reverse-transition>
                  <v-btn
                    v-show="hover"
                    icon
                    color="error"
                    :loading="item.delete"
                    @click="deleteItem(item.id, index)"
                  >
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
import axios from "axios";
export default {
  data: () => ({
    items: []
  }),
  created: function() {
    this.loadlist();
  },
  methods: {
    async loadlist() {
      this.items = [];
      try {
        const res = await axios.get("http://localhost:3000/pending/");
        res.data.map(({ id, title, desc, icon, time, date, done }) => {
          this.items.push({
            id: id,
            title: title,
            desc: desc,
            time: time,
            date: date,
            icon: icon,
            done: done,
            delete: false
          });
        });
        let complete = 0,
          total = res.data.length;
        res.data.map(({ done }) => {
          if (done === true) {
            complete++;
          }
        });
        this.$emit("dataLoaded", { complete: complete, total: total });
      } catch (error) {
        alert("Couldn't load data from the database");
      }
    },
    async completeItem(task_id, index) {
      this.items[index].done = true;
      const { id, title, desc, icon, time, date, done } = this.items[index];
      try {
        const res = await axios.put(
          `http://localhost:3000/pending/${task_id}`,
          {
            id: id,
            title: title,
            desc: desc,
            time: time,
            date: date,
            icon: icon,
            done: done
          }
        );
        this.$emit("taskStateChanged", id);
        console.log(res.data);
      } catch (err) {
        console.log(err);
      }
    },
    async deleteItem(task_id, index) {
      this.items[index].delete = true;
      const { id, title, desc, icon, time, date, done } = this.items[index];
      try {
        const res = axios.post("http://localhost:3000/aborted", {
          id: id,
          title: title,
          desc: desc,
          time: time,
          date: date,
          icon: icon,
          done: done
        });
        console.log(res.data);
        await axios.delete(`http://localhost:3000/pending/${task_id}`);
        this.$emit("itemDeleted", task_id);
        this.items[index].delete = false;
        this.loadlist();
      } catch (error) {
        console.log(error);
      }
    }
  },
  computed: {
    //
  }
};
</script>