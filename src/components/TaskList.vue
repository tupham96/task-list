<template>
  <div class="list-task">{{filterText}}
    <div class="list-header d-flex align-items-center">
      <h4>Task list</h4>
      <p v-if="tab != 3" class="list-status ml-auto">Completed {{ completedTask }}%</p>
      <p v-else class="list-status ml-auto">{{ listTasks.length }} Tasks</p>
    </div>
    <div class="list-items">
      <task-item
        v-for="task in filterTasks"
        :item="task"
        :key="task.id"
        @updateTask="triggerEdit($event)"
        @refreshTask="triggerRefresh()"
      ></task-item>
    </div>
    <button v-if="!isShowed && tab != 2" class="btn border-0 btn-add mt-3" @click="isShowed = !isShowed">
      <i class="fas fa-plus"></i> Add task
    </button>
    <div class="action" v-if="isShowed">
      <hr />
      <input type="text" class="form-control mr-2" placeholder="Task title" v-model="taskTitle" />
      <button v-if="!isUpdate" class="btn btn-add-task mr-2 mt-3" @click="addTask()">
        <i class="fas fa-plus"></i> Add task
      </button>
      <button v-else class="btn btn-update-task mr-2 mt-3" @click="updateTask(taskId, taskTitle)">
        <i class="fas fa-plus"></i> Update task
      </button>
      <button class="btn btn-cancel mt-3" @click="isShowed = !isShowed">Cancel</button>
    </div>
  </div>
</template>

<script>
import TaskItem from "./TaskItem";
import axios from "axios";

export default {
  components: {
    taskItem: TaskItem
  },
  props: ["tab", "filterText"],
  data: function() {
    return {
      isShowed: false,
      taskTitle: "",
      taskId: "",
      isUpdate: false,
      listTasks: [],
      completedTask: 0
    };
  },
  methods: {
    calculatePercentage(list) {
      var number = list.filter(x => x.is_completed == 1).length;
      return Math.round((number/list.length)*100).toFixed(0);
    },
    triggerEdit(value) {
      this.isShowed = true;
      this.isUpdate = true;
      this.taskTitle = value.title;
      this.taskId = value.id;
    },
    async addTask() {
      this.isShowed = false;
      try {
        await axios({
          method: "POST",
          url: "https://tasking-ltl.herokuapp.com/v1/graphql",
          data: {
            query: `
            mutation addTask($title: String!) {
                insert_task_one(object: {
                    title: $title
                }
                ){
                    id
                }
            }`,
            variables: {
              title: this.taskTitle
            }
          }
        }).then(response => {
          this.getTasks(this.tab);
          this.taskTitle = "";
        });
      } catch (error) {
        console.error(error);
      }
    },
    async updateTask(id, title) {
      this.isShowed = false;
      try {
        const result = await axios({
          method: "POST",
          url: "https://tasking-ltl.herokuapp.com/v1/graphql",
          data: {
            query: `
            mutation updateTask($id: Int!, $title: String!) {
                update_task(
                    where: {
                        id:{
                            _eq: $id
                        }
                    },
                    _set: {
                        title: $title
                    }
                ){
                    affected_rows
                }
            }`,
            variables: {
              id: id,
              title: title
            }
          }
        }).then(response => {
          this.getTasks(this.tab);
        });
      } catch (error) {
        console.error(error);
      }
    },

    async getTasks(tab) {
      let data = "";
      if (tab === 1) {
        try {
          var result = await axios({
            method: "POST",
            url: "https://tasking-ltl.herokuapp.com/v1/graphql",
            data: {
              query: `
            {
                task {
                    id
                    title
                    is_completed
                }
            }`
            }
          });
          this.listTasks = result.data.data.task;
          this.completedTask = this.calculatePercentage(this.listTasks);
        } catch (error) {
          console.error(error);
        }
      } else if (tab === 2) {
        try {
          var result = await axios({
            method: "POST",
            url: "https://tasking-ltl.herokuapp.com/v1/graphql",
            data: {
              query: `
            {
                task (
                    where: {
                        is_completed:{
                            _eq: true,
                        }
                    })
                {
                    id
                    title
                    is_completed
                }
            }`
            }
          });
          this.listTasks = result.data.data.task;
          this.completedTask = this.calculatePercentage(this.listTasks);
        } catch (error) {
          console.error(error);
        }
      } else {
        try {
          var result = await axios({
            method: "POST",
            url: "https://tasking-ltl.herokuapp.com/v1/graphql",
            data: {
              query: `
          {
            task (
                where: {
                    is_completed:{
                        _eq: false
                    }
                })
            {
                id
                title
                is_completed
            }
        }`
            }
          });
          this.listTasks = result.data.data.task;
          this.completedTask = this.calculatePercentage(this.listTasks);
        } catch (error) {
          console.error(error);
        }
      }
    },

    triggerRefresh() {
      this.getTasks(this.tab);
    }
  },
  mounted() {
    this.getTasks(this.tab);
  },
  computed: {
      filterTasks() {
          return this.listTasks.filter((element)=> {
            return element.title.toLowerCase().match(this.filterText.toLowerCase().trim());
          });
      }
  },
};
</script>

<style lang="scss">
.list-task {
  margin-top: 50px;

  h4 {
    font-size: 20px;
    line-height: 23px;
    color: #2d2d2d;
  }

  .list-status {
    font-size: 14px;
    line-height: 16px;
    color: #7e7e7e;
  }

  .btn-add {
    background: transparent;
    font-size: 16px;
    line-height: 19px;

    i {
      color: #8c8c8c;
      margin-right: 5px;
    }
  }

  hr {
    background: #ececec;
  }

  .btn-add-task,
  .btn-update-task {
    background: #d65d5d !important;
    border-radius: 4px;
    font-size: 16px;
    color: #ffffff;
  }

  .btn-add-task:hover,
  .btn-update-task:hover {
    color: #fff;
  }

  .btn-cancel {
    background: #f0f0f0;
  }
}
</style>