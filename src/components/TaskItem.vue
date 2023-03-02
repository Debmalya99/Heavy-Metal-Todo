<template>

<div class="accordion-item">
    <h2 class="accordion-header" id="headingOne">
      <button class="accordion-button" type="button" data-bs-toggle="collapse" data-bs-target="#collapseOne" aria-expanded="true" aria-controls="collapseOne" @click="showDescription = !showDescription"> 
        <input type="checkbox" id="checkbox" v-model="task.itemStatus" />
        <span :class="{is_done:task.itemStatus}">{{task.itemTitle}}</span>
        (Weightage: {{task.itemPriority}})
        <span @click="moveToDeleted" style="padding-left:15px;"><i class="gg-trash"></i></span>
        <span style="padding-left:15px;" @click="editDescription = !editDescription">
          <i class="gg-pen" v-if="!editDescription"></i>
          <i class="gg-calendar-due" v-else @click="updated()"></i>
        </span>
      </button>
    </h2>
    <div id="collapseOne" class="accordion-collapse collapse show" aria-labelledby="headingOne" data-bs-parent="#accordionExample">
      <div class="accordion-body" v-show="showDescription">
        <!-- Status: <input type="checkbox" id="checkbox" v-model="task.itemStatus" /> -->
        Project: {{task.itemProject}}
        <hr/>
        Description: <span v-if="!editDescription">{{task.itemDesc}}</span> 
        <textarea v-else v-model="task.itemDesc"></textarea>
      </div>
    </div>
  </div>

  <!-- <div class="accordion-item">
    <div>
      <div>
        <div class="clickable" @click="showDescription = !showDescription">
          <input
            class="clickable"
            :disabled="!editTitle"
            type="text"
            v-model="task.itemTitle"
            @blur.stop="editTitle = false"
          />
        </div>
        <button v-show="!showDescription" @click="editTitle = !editTitle">
          {{ editTitleButton }}
        </button>
      </div>
    </div>
    <div v-show="showDescription">
      <textarea
        :disabled="!editDescription"
        v-model="task.itemDesc"
        @blur.stop="editDescription = false"
      ></textarea>
    </div>
    <div v-show="showDescription">
      Priority: {{task.itemPriority}} 
    </div>
    {{ task.itemStatus }}
    <button
      v-show="showDescription"
      @click="editDescription = !editDescription"
    >
      {{ editDescriptionButton }}
    </button>
    <button @click="setStatusButton">{{ statusButton }}</button>
    <button @click="moveToDeleted">Delete</button>
  </div> -->
</template>

<script>
import '../../node_modules/bootstrap/dist/css/bootstrap.min.css';
import '../../node_modules/css.gg/icons/all.css';


export default {
  name: "TaskItem",
  props: {
    taskDetails: {
      taskTitle: String,
      taskDescription: String,
      // taskStatus: String,
      taskStatus: Boolean,
      taskPriority:Number,
      taskId: Number,
      taskProject:String,
    }
  },
  data() {
    return {
      showDescription: false,
      editDescription: false,
      editTitle: false,
      task: {
        itemTitle: this.taskDetails.taskTitle,
        itemDesc: this.taskDetails.taskDescription,
        itemStatus: this.taskDetails.taskStatus,
        itemPriority: this.taskDetails.taskPriority,
        itemId: this.taskDetails.taskId,
        itemProject: this.taskDetails.taskProject
      },
      taskStatusBoolean:false,
      
    };
  },
  methods: {
    setStatusButton() {
      

      this.task.itemStatus = this.task.itemStatus === "todo" ? "done" : "todo";
    },
    moveToDeleted() {
      this.task.itemStatus = "deleted";
    }
  },
  updated() {
    this.$emit("task-update", this.task);
  },
  computed: {
    editTitleButton() {
      return this.editTitle ? "save" : "edit";
    },
    editDescriptionButton() {
      return this.editDescription ? "save" : "edit";
    },
    statusButton() {
      return this.task.itemStatus === "todo" ? "Done" : "Todo";
    }
  }
};
</script>

<style scoped>
.clickable {
  cursor: pointer;
}
.todo {
  color: red;
}

.is_done{
  text-decoration: line-through;
}
</style>
