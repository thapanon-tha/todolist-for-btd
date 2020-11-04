<template>
  <div class="control">
    <button class="button is-fullwidth is-success" @click="addModel = true">
      <span class="icon">
        <i class="fas fa-plus"></i>
      </span>
      <span>Add Activity</span>
    </button>

    <b-modal v-model="addModel" :width="640">
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
                  v-model="input.workName"
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
                  v-model="input.workDate"
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
              v-model="input.workDetail"
              placeholder="Your activity detail"
            ></b-input>
          </div>
        </div>
        <div class="modal-card-foot">
          <!-- <div class="control"> -->
          <div class="field">
            <button class="button is-success" @click="checkSpace()">
              Done
            </button>
            <button
              class="button is-danger"
              type="button"
              @click="addModel = false"
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
export default {
  name: "addtodoModel",
  data() {
    const today = new Date();
    return {
      input: {
        workName: "",
        workDetail: "",
        workDate: new Date(),
        workDateforCal: 0,
      },
      addModel: false,
      minDate: new Date(today.getFullYear(), today.getMonth(), today.getDate()),
    };
  },
  props: {
    todoList: Array,
    completeList: Array
  },
  methods: {
    checkSpace() {
      if (this.input.workName === undefined || this.input.workName === "") {
        this.massageDanger("please fill <b>Activity Name</b>");
      } else {
        if (
          this.input.workDate === undefined ||
          this.input.workDate === null ||
          this.input.workName === ""
        ) {
          this.massageDanger("please fill <b>Activity Date</b>");
        } else {
          this.input.workDateforCal = +new Date(this.input.workDate);
          console.log(this.input.workDate);
          console.log(this.input);
          this.submitAvtivity();
        }
      }
    },
    submitAvtivity() {
      let max=0;
      if (this.todoList.length === 0 && this.completeList.length === 0 ) {
        this.input.id = 1;
      } 
      else {
        for(let i=0;i<this.todoList.length;i++){
          if(max<this.todoList[i].id){
            max = this.todoList[i].id;
          }
        }
        for(let j=0;j<this.completeList.length;j++){
          if(max<this.completeList[j].id){
            max = this.completeList[j].id;
          }
        }
        this.input.id = max + 1;
      }
      this.todoList.push({ ...this.input });
      console.log("666666666666666666666666666666666");
      this.$emit('add:activity', { id: 1 } );
      console.log("666666666666666666666666666666666");
      this.input = {};
      this.addModel = false;
    },
    massageDanger(msg) {
      this.$buefy.toast.open({
        duration: 5000,
        message: msg,
        position: "is-top",
        type: "is-danger",
      });
    },
  },
};
</script>

<style>
.card {
  overflow: visible;
}
.modal-content,
.modal-card,
.modal-card-body {
  overflow: visible !important;
}
</style>