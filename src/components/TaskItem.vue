<template>
  <div class="list-item d-flex align-items-center" v-bind:class="{ 'bg-green': item.is_completed }">
    <button
      v-if="!item.is_completed"
      class="btn btn-unchecked"
      @click="tickTask(item.id, !item.is_completed)"
    >
      <i class="far fa-circle"></i>
    </button>
    <button v-else class="btn btn-checked" @click="tickTask(item.id, !item.is_completed)">
      <i class="far fa-check-circle"></i>
    </button>
    <span class="ml-2">{{ item.title }}</span>
    <button
      v-show="!item.is_completed"
      class="ml-auto btn-option"
      @click="showAction = !showAction"
    >
      <i class="fas fa-ellipsis-h"></i>
    </button>
    <ul v-if="!item.is_completed && showAction" class="item-actions">
      <li class="item-action" @click="updateTask(item.id, item.title)">
        <i class="fas fa-pencil-alt"></i> Edit
      </li>
      <li class="item-action" @click="deleteTask(item.id)">
        <i class="far fa-trash-alt"></i> Delete
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: ["item"],
  data: function() {
    return {
      showAction: false
    };
  },
  methods: {
    updateTask(id, title) {
      this.showAction = false;
      console.log(id);
      this.$emit("updateTask", { id: id, title: title });
    },
    async deleteTask(id) {
      this.showAction = false;

      try {
        await axios({
          method: "POST",
          url: "https://tasking-ltl.herokuapp.com/v1/graphql",
          data: {
            query: `
            mutation deleteTask($id: Int!) {
                delete_task_by_pk(id: $id){
                    id
                }
            }`,
            variables: {
              id: id
            }
          }
        }).then(response => {
            console.log(response);
            this.$emit("refreshTask", "");
        });
      } catch (error) {
        console.error(error);
      }
    },

    async tickTask(id, is_completed) {
      this.showAction = false;

      try {
        await axios({
          method: "POST",
          url: "https://tasking-ltl.herokuapp.com/v1/graphql",
          data: {
            query: `
            mutation updateTask($id: Int!, $is_completed: Boolean!) {
                update_task(
                    where: {
                        id:{
                            _eq: $id
                        }
                    },
                    _set: {
                        is_completed: $is_completed
                    }
                ){
                    affected_rows
                }
            }`,
            variables: {
              id: id,
              is_completed: is_completed
            }
          }
        }).then(response => {
            this.$emit("refreshTask", "");
        });
      } catch (error) {
        console.error(error);
      }
    }
  }
};
</script>

<style lang="scss">
.list-item {
  background: rgba(162, 162, 162, 0.06);
  border-radius: 4px;
  padding: 10px;
  width: 100%;
  margin-top: 5px;
  margin-bottom: 5px;
  position: relative;

  .btn {
    background: transparent;
    border: none;
    padding: 0;
  }

  .btn-checked {
    i {
      color: #37aa43;
    }
  }
  .btn-unchecked {
  }

  .btn-unchecked:hover {
    i {
      color: #37aa43;
    }
  }

  .btn-option {
    background: #e4e4e4;
    border-radius: 3px;
    border: none;
  }

  .item-actions {
    background: #fff;
    position: absolute;
    top: 80%;
    right: 10px;
    z-index: 99;
    padding: 0;

    .item-action {
      padding: 10px;
      font-size: 16px;
      line-height: 19px;
      list-style-type: none;
    }

    .item-action:hover {
      cursor: pointer;
      color: #e87070;
    }
  }
}

.bg-green {
  background: rgba(121, 192, 4, 0.06);
}
</style>