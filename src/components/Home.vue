<template>
  <div class="home-container d-flex justify-content-center flex-column">
    <div class="filter-bar d-flex">
      <input type="text" class="form-control mr-2" placeholder="Filter" v-model="filterText"/>
      <button @click="activate(1)" :class="{ active: activeElement == 1 }" class="btn mx-2">All</button>
      <button @click="activate(2)" :class="{ active: activeElement == 2 }" class="btn mx-2">Done</button>
      <button
        @click="activate(3)"
        :class="{ active: activeElement == 3 }"
        class="btn mx-2"
      >Incompleted</button>
    </div>
    <task-list :tab="activeElement" :filterText="filterText" ref="taskList"></task-list>
  </div>
</template>

<script>
import TaskList from "./TaskList";

export default {
  components: {
    taskList: TaskList
  },
  data: function() {
    return {
      activeElement: 1,
      filterText: '',
    };
  },
  methods: {
    activate: function(el) {
      if(!this.activeElement) {
        this.activeElement = 1;
      }
      this.activeElement = el;
      this.$refs.taskList.getTasks(this.activeElement);
    }
  },
};
</script>

<style lang="scss">
.home-container {
  margin-top: 40px;
  .form-control {
    font-size: 16px;
    line-height: 19px;
    color: #c0c0c0;
  }
  .btn {
    background: #dcdcdc;
  }

  .btn.active {
    background: #dc8080;
    color: #fff;
  }
}
</style>