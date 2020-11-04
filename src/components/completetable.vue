<template>
  <div class="control">
    <div class="title">
      <p class="title">Complete Activity</p>
    </div>
    <b-field>
      <b-input
        placeholder="Search..."
        type="search"
        icon="magnify"
        v-model="name"
        icon-clickable
      >
      </b-input>
    </b-field>
      <div v-for="(complete, j) in resultQuery" :key="j" :value="j">
        <div class="card" style="margin: 8px">
          <div>
            <header class="card-header">
              <p class="card-header-title">
                  
                <span class="icon"><i class="far fa-calendar-check"></i></span
                >{{ complete.workName }}
              </p>
              <div class="filed" style="padding: 0.75rem 1rem">
                <span class="tag is-success">{{
                  getDateString(new Date(complete.workDateforCal), "d-M-y")
                }}</span>
              </div>
            </header>
            <div class="card-content">
              <div class="content">
                <div>
                  {{ complete.workDetail }}
                  <br />
                </div>
              </div>
            </div>
            <footer class="card-footer">
              <a class="card-footer-item"
                ><button
                  class="button is-warning is-small"
                  @click="getIndex(complete.id)"
                >
                  <span class="icon">
                    <i class="fas fa-clipboard-check"></i>
                  </span></button
              ></a>
            </footer>
          </div>
        </div>
      </div>
  </div>
</template>

<script>
export default {
  name: "completeTable",
  computed: {
    resultQuery() {
      if (this.name) {
        if (this.completeList.length > 0) {
          return this.completeList.filter((item) => {
            return this.name
              .toLowerCase()
              .split(" ")
              .every((v) => item.workName.toLowerCase().includes(v));
          });
        } else {
          return this.completeList;
        }
      } else {
        return this.completeList;
      }
    },
  },
  props: {
    completeList: Array,
  },
  methods: {
    getIndex(index) {
      console.log("check index" + index);
      this.$emit("uncheck:activity", { id: index });
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
