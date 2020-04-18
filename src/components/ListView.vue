<template>
  <v-container fluid class="pa-1 mt-n2">
    <v-list three-line subheader shaped elevation="3">
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
                    @click="confirmDeleteion(item.id, index)"
                  >
                    <v-icon>mdi-close-circle</v-icon>
                  </v-btn>
                </v-slide-x-reverse-transition>
              </v-row>
            </v-list-item-action>
          </v-list-item>
        </v-hover>
      </v-slide-x-transition>
    </v-list>
    <!-- error conformation dialogue box -->
    <v-dialog
      v-model="dialog"
      persistent
      :overlay="false"
      max-width="400px"
      dense
      transition="dialog-transition"
    >
      <v-card>
        <v-card-title class="error--text">Are you sure!</v-card-title>
        <v-card-text>You want to delete the following task</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            dark
            depressed
            :loading="del"
            color="error"
            @click="deleteItem(deleteId, deletindex)"
          >Delete</v-btn>
          <v-btn outlined depressed color="error" @click="dialog = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- snackbar -->
    <v-snackbar color=" error" class="white--text" v-model="snackbar" :timeout="2500">
      1 task deleted
      <v-btn outlined class="error white--text" @click="snackbar = false">Close</v-btn>
    </v-snackbar>
  </v-container>
</template>
<script>
import axios from "axios";

export default {
  name: "ListView",
  components: {},
  data: () => ({
    items: [],
    dialog: false, // for dialogue box
    deleteId: -1,
    deletindex: -1,
    del: false, // for dialogue's delete button loading  animation
    snackbar: false // for snackbar prompt handling
  }),
  created: function() {
    this.loadlist();
  },
  methods: {
    confirmDeleteion(task_id, index) {
      this.dialog = true;
      this.deleteId = task_id;
      this.deletindex = index;
    },
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
            done: done
          });
        });
        const ab_res = await axios.get("http://localhost:3000/aborted/");
        let complete = 0,
          total = res.data.length;
        res.data.map(({ done }) => {
          if (done === true) {
            complete++;
          }
        });
        this.$emit("dataLoaded", {
          complete: complete,
          total: total,
          abort: ab_res.data.length
        });
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
      this.del = true;
      const { id, title, desc, icon, time, date, done } = this.items[index];
      const obj = {
        id: id,
        title: title,
        desc: desc,
        time: time,
        date: date,
        icon: icon,
        done: done
      };
      try {
        if (this.items[index].done === true) {
          await axios.post("http://localhost:3000/completed", obj);
        } else {
          axios.post("http://localhost:3000/aborted", obj);
          this.$emit("itemDeleted", task_id);
        }
        this.dialog = false;
        this.del = false;
        await axios.delete(`http://localhost:3000/pending/${task_id}`);
        this.loadlist();
        this.snackbar = true;
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