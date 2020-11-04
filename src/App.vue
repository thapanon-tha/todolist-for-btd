<template>
  <div id="app">
    <div class="container">
      <div class="columns is-centered">
        <div class="column is-6">
          <addtodoModel
            v-bind:todoList="todoList"
            v-bind:completeList="completeList"
            @add:activity="sortActivity"
          />
        </div>
      </div>
      <b-tabs position="is-centered" v-model="activeTab">
        <b-tab-item label="Today Activity" class="is-centered">
          <div class="columns is-centered">
            <div class="column is-6">
              <div style="margin: 20px" />
              <todayTable
                v-bind:todayList="todayList"
                @delete:activity="deleteActivityMassage"
                @check:activity="checkActivity"
                @edit:activity="editActivity"
              />
            </div>
          </div>
        </b-tab-item>

        <b-tab-item label="All Activity">
          <div class="columns">
            <div class="column">
              <div style="margin: 20px" />
              <todoTable
                class="is-half"
                v-bind:todoList="todoList"
                @delete:activity="deleteActivityMassage"
                @check:activity="checkActivity"
                @edit:activity="editActivity"
              />
            </div>
            <div class="column">
              <div style="margin: 20px" />
              <completeTable
                v-bind:completeList="completeList"
                @uncheck:activity="cancalActivity"
              />
            </div>
          </div>
        </b-tab-item>
      </b-tabs>
    </div>

    <b-modal v-model="editVar" :width="640">
      <div class="card">
        <header class="modal-card-head">
          <div class="title is-5 level-left">
            <span class="icon">
              <i class="fas fa-plus"></i>
            </span>
            เพิ่มสิ่งที่ต้องทำ
          </div>
        </header>
        <div class="modal-card-body">
          <div class="columns">
            <div class="column">
              <div class="field">
                <label class="label level-left">Activity Name</label>
                <b-input
                  type="input"
                  v-model="newName"
                  placeholder="Your activity name"
                >
                </b-input>
              </div>
            </div>
            <div class="column">
              <div class="filde">
                <label class="label level-left">Dead Line</label>
                <b-datepicker
                  placeholder="select a date..."
                  icon="calendar-today"
                  :min-date="minDate"
                  v-model="newDate"
                  editable
                >
                </b-datepicker>
              </div>
            </div>
          </div>
          <div class="field">
            <label class="label level-left">Detail</label>
            <b-input
              maxlength="300"
              type="textarea"
              v-model="newDetail"
              placeholder="Your activity detail"
            ></b-input>
          </div>
        </div>
        <div class="modal-card-foot">
          <!-- <div class="control"> -->
          <div class="field">
            <button class="button is-success" @click="editSubmit()">
              Done
            </button>
            <button
              class="button is-danger"
              type="button"
              @click="editVar = false"
            >
              Close
            </button>
            <!-- </div> -->
          </div>
        </div>
      </div>
    </b-modal>
  </div>
</template>

<script>
import navbar from "@/components/navbar";
import todoTable from "@/components/todo-table";
import addtodoModel from "@/components/addtodoModel";
import todayTable from "@/components/todayTable";
import completeTable from "@/components/completetable";

export default {
  name: "App",
  data() {
    let today = new Date();
    return {
      activeTab: 0,
      name: "",
      todoList: [],
      completeList: [],
      addModel: false,
      todayList: [],
      addSortVar: 0,
      editVar: false,
      newName: "",
      newDate: new Date(),
      newDetail: "",
      minDate: new Date(today.getFullYear(), today.getMonth(), today.getDate()),
      editIndex: Number,
    };
  },
  components: {
    navbar,
    todoTable,
    addtodoModel,
    todayTable,
    completeTable,
  },
  mounted() {
    if (localStorage.list) {
      console.log("////////////////////////////");
      this.todoList = JSON.parse(localStorage.list);
      console.log(this.todoList);
    }
    if (localStorage.complete) {
      console.log("////////////////////////////");
      this.completeList = JSON.parse(localStorage.complete);
      console.log(this.completeList);
    }
  },

  watch: {
    todoList(todoList) {
      localStorage.list = JSON.stringify(this.todoList);
      this.findtodaylist();
    },
    completeList(completeList) {
      localStorage.complete = JSON.stringify(this.completeList);
    },
  },
  methods: {

    deleteActivityMassage(index){
      this.massageSuccess("Delete activity <b>Success</b>");
      deleteActivity(index);
    },


    deleteActivity(index) {
      if (this.todoList.length > 1) {
        this.todoList = this.todoList.filter(function (activity) {
          return activity.id !== index.id;
        });
        // localStorage.list = JSON.stringify(this.todoList);
      } else {
        this.todoList = [];
        localStorage.list = [];
      }
      
    },

    checkActivity(index) {
      let activity = this.todoList.filter(function (activity) {
        return activity.id === index.id;
      });
      console.log(activity);
      this.completeList.push(...activity);
      this.completeList.sort(this.dynamicSort("-workDateforCal"));
      this.deleteActivity(index);
      this.massageSuccess("Check activity <b>Success</b>!!");
    },

    cancalActivity(index) {
      let activity = this.completeList.filter(function (activity) {
        return activity.id === index.id;
      });
      console.log(activity);
      this.todoList.push(...activity);
      this.todoList.sort(this.dynamicSort("workDateforCal"));
      this.moveActivity(index);
    },

    moveActivity(index) {
      if (this.completeList.length > 1) {
        this.completeList = this.completeList.filter(function (activity) {
          return activity.id !== index.id;
        });
        // localStorage.list = JSON.stringify(this.todoList);
      } else {
        this.completeList = [];
        localStorage.complete = [];
      }
      this.massageSuccess("cancal activity <b>Success</b>");
    },

    editActivity(index) {
      this.editVar = true;
      console.log(index.id);
      let activity = this.todoList.filter(function (activity) {
        return activity.id === index.id;
      });
      this.newName = activity[0].workName;
      this.newDetail = activity[0].workDetail;
      this.newDate = new Date(activity[0].workDate);
      this.editIndex = index.id;
    },

    editSubmit() {
      for (var i in this.todoList) {
        if (this.todoList[i].id == this.editIndex) {
          this.todoList[i].workName = this.newName;
          this.todoList[i].workDetail = this.newDetail;
          this.todoList[i].workDate = this.newDate;
          this.todoList[i].workDateforCal = +new Date(this.newDate);
          break; //Stop this loop, we found it!
        }
      }
      this.editVar = false;
      this.todoList.sort(this.dynamicSort("workDateforCal"));
      localStorage.list = JSON.stringify(this.todoList);
      this.massageSuccess("edit activity <b>Success</b>");
    },

    sortActivity(indexx) {
      console.log("///////////||||||||\\\\\\\\");
      this.addSortVar = indexx.id;
      console.log(this.addSortVar);
      if (this.addSortVar === 1) {
        this.todoList.sort(this.dynamicSort("workDateforCal"));
      }
      this.addSortVar = 0;
    },

    findtodaylist() {
      let today = new Date();
      let tomorrow = +new Date(
        today.getFullYear(),
        today.getMonth(),
        today.getDate() + 1
      );
      let yesterday = +new Date(
        today.getFullYear(),
        today.getMonth(),
        today.getDate() - 1
      );
      this.todayList = this.todoList.filter(function (activity) {
        if (
          activity.workDateforCal > yesterday &&
          activity.workDateforCal < tomorrow
        )
          return true;
        else return false;
      });
    },

    dynamicSort(property) {
      var sortOrder = 1;
      if (property[0] === "-") {
        sortOrder = -1;
        property = property.substr(1);
      }
      return function (a, b) {
        var result =
          a[property] < b[property] ? -1 : a[property] > b[property] ? 1 : 0;
        return result * sortOrder;
      };
    },

    massageDanger(msg) {
      this.$buefy.toast.open({
        duration: 3000,
        message: msg,
        position: "is-top",
        type: "is-danger",
      });
    },
    
    massageSuccess(msg) {
      this.$buefy.toast.open({
        duration: 3000,
        message: msg,
        position: "is-top",
      });
    },


  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
