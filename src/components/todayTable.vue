<template>
  <div class="control">
    <div class="title">
      <p class="title">Today Activity</p>
    </div>
    <b-field>
      <div style="margin: 40px"></div>
    </b-field>
    <div v-if="todayList.length">
      <div v-for="(todayList, j) in todayList" :key="j" :value="j">
        <div class="card" style="margin: 8px">
          <div>
            <header class="card-header">
              <p class="card-header-title">
                <span class="icon"><i class="fas fa-paste"></i></span
                >{{ todayList.workName }}
              </p>
              <div class="filed" style="padding: 0.75rem 1rem">
                <span class="tag is-warning">{{
                  getDateString(new Date(todayList.workDateforCal), "d-M-y")
                }}</span>
              </div>
            </header>
            <div class="card-content">
              <div class="content">
                <div>
                  {{ todayList.workDetail }}
                  <br />
                </div>
              </div>
            </div>
            <footer class="card-footer">
              <a class="card-footer-item"
                ><button
                  class="button is-success is-small"
                  @click="getIndex(todayList.id, 3)"
                >
                  <span class="icon">
                    <i class="fas fa-check"></i>
                  </span></button
              ></a>
              <a class="card-footer-item"
                ><b-button
                  class="button is-warning is-small"
                  @click="getIndex(todayList.id, 1)"
                >
                  <span class="icon">
                    <i class="fas fa-pen"></i>
                  </span> </b-button
              ></a>
              <a class="card-footer-item"
                ><button
                  class="button is-danger is-small"
                  @click="getIndex(todayList.id, 2)"
                >
                  <span class="icon">
                    <i class="fas fa-trash-alt"></i>
                  </span></button
              ></a>
            </footer>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "todoTable",
  computed: {
  },
  props: {
    todayList: Array,
    // completeList: Array,
  },
  methods: {
    getIndex(index, mode) {
      console.log("index : " + index + "Mode :" + mode);
      if (mode === 2) {
        this.deleteActivity(index);
      }
      if(mode === 3){
        this.checkActivity(index);
      }
      if(mode === 1){
        this.editActivity(index);
      }
    },

    deleteActivity(index) {
      this.$emit("delete:activity", { id: index });
      console.log(this.resultQuery);
    },
    

    checkActivity(index) {
      console.log("check index" + index);
      this.$emit("check:activity", { id: index });
    },

    editActivity(index) {
      this.$emit("edit:activity", { id: index });
    },

    getDateString(date, format) {
      var months = [
          "Jan",
          "Feb",
          "Mar",
          "Apr",
          "May",
          "Jun",
          "Jul",
          "Aug",
          "Sep",
          "Oct",
          "Nov",
          "Dec",
        ],
        getPaddedComp = function (comp) {
          return parseInt(comp) < 10 ? "0" + comp : comp;
        },
        formattedDate = format,
        o = {
          "y+": date.getFullYear(), // year
          "M+": months[date.getMonth()], //month
          "d+": getPaddedComp(date.getDate()), //day
          "h+": getPaddedComp(
            date.getHours() > 12 ? date.getHours() % 12 : date.getHours()
          ), //hour
          "H+": getPaddedComp(date.getHours()), //hour
          "m+": getPaddedComp(date.getMinutes()), //minute
          "s+": getPaddedComp(date.getSeconds()), //second
          "S+": getPaddedComp(date.getMilliseconds()), //millisecond,
          "b+": date.getHours() >= 12 ? "PM" : "AM",
        };
      for (var k in o) {
        if (new RegExp("(" + k + ")").test(format)) {
          formattedDate = formattedDate.replace(RegExp.$1, o[k]);
        }
      }
      return formattedDate;
    },

    
  },
  data() {
    return {
      name: "",
    };
  },
};
</script>

<style scoped>
</style>
