<!-- https://codepen.io/AndrewThian/pen/QdeOVa -->

<template>
  <div v-on:keyup.191="console.log('Heyo')">

    <nav class="navbar navbar-expand-lg bg-light">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">Heavy Metal Todos</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">
        <li class="nav-item">
          <span class="nav-link" aria-current="page" @click="filterBy = 'all'">All</span>
        </li>
        <li class="nav-item">
          <span class="nav-link" @click="filterBy = false">To Do</span>
        </li>

        <li class="nav-item">
          <span class="nav-link" @click="filterBy = true">Done</span>
        </li>
        
        
      </ul>
      <form class="d-flex" role="search"> 
        <input class="form-control me-2" type="search" placeholder="Press / to search" aria-label="Search" v-model="searchText" ref="searchbar">
        
      </form>
    </div>
  </div>
</nav>

<!-- <p>{{tasksToShow}}</p> -->
  <!-- <button @click="test()">Test button</button> -->
    <new-task :project-list="projectList" @create-new-task="createNewTask"></new-task>
    <!-- <span></span> -->
    <div class="card">
      <div class="card-body">
        Completed: {{getCompletionPercentage}}%
      </div>
    </div>
    <div class="container row">
      <div class="accordion container-fluid col">
      <div v-for="task in tasksToShow" :key="task.taskId">
      <task-item
                :task-details="task"
        @task-update="updateATask"
      ></task-item>
      </div>
      </div>
      <div class="col col-lg-4">
        <h4>Filter by Projects:</h4>
        <select class="form-select" aria-label="Default select example" v-model="currentProject">
          <!-- <option selected>Filter by Project</option> -->
          
          <option v-for="proj in projectList" :key="proj">
            {{proj}}
          </option>
        </select>
        <h4>Add a new project:</h4>
        <div class="mb-3">
          
          <input class="form-control" id="exampleFormControlInput1" placeholder="Enter project name" v-model="projectName">
          <div class="col-auto">
            <button type="submit" class="btn btn-primary mb-3" @click="addProject()">Add Project</button>
          </div>
          
        </div>
        <timer-card/>
      </div>
    </div>
    
    

  </div>
</template>

<script>
import TaskItem from "./TaskItem";
import NewTask from "./NewTask";
import TimerCard from "./TimerCard.vue";
import '../../node_modules/bootstrap/dist/css/bootstrap.min.css';
// const alarm_audio = require('../assets/alarm-clock-short-6402.mp3');

export default {
  name: "TodoList",
  components: { NewTask, TaskItem, TimerCard },
  props: {
    // msg: String
  },
  data() {
    return {
      tasks: [],
      filterBy: "all",
      searchText:"",
      projectName:"",
      projectList:["all"],
      currentProject:"all",
    };
  },
  mounted() {
    this.getFromLocalStorage();
  },
  created(){
    window.addEventListener('keydown',e=>{
      if(e.key === '/'){
        this.$refs.searchbar.focus();
      }
    })
  },
  methods: {
    createNewTask(task) {
      // adds a new task to the array
      let newTask = {
        taskTitle: task.newTaskTitle,
        taskDescription: task.newTaskDesc,
        taskStatus: false,
        taskPriority:task.newTaskPriority,
        taskId: this.newTaskId,
        taskProject: task.projectName
      };
      if (newTask.taskTitle.length > 0) {
        this.tasks.push(newTask);
        this.saveToLocalStorage();
      }
    },
    updateATask(updatedTask) {
      this.tasks.forEach(task => {
        if (task.taskId === updatedTask.itemId) {
          task.taskTitle = updatedTask.itemTitle;
          task.taskDescription = updatedTask.itemDesc;
          task.taskStatus = updatedTask.itemStatus;
        }
      });
      this.saveToLocalStorage();
    },
    getFromLocalStorage() {
      if (localStorage.tasks) this.tasks = JSON.parse(localStorage.getItem("tasks"));
      if(localStorage.projects) this.projectList = JSON.parse(localStorage.getItem("projects"));
    },
    saveToLocalStorage() {
      localStorage.setItem("tasks",JSON.stringify(this.tasks));
      localStorage.setItem("projects",JSON.stringify(this.projectList));
    },
    addProject(){
      // console.log("Added a project "+this.projectName);
      this.projectList.push(this.projectName);
      this.saveToLocalStorage();
      this.projectName = "";
    },
    
  },
  computed: {
    newTaskId() {
      return (
        this.tasks.reduce((max, curr) => Math.max(max, curr.taskId), 0) + 1
      );
    },
    tasksToShow() {
      let task_1 = this.filterBy === "all"
        ? this.tasks.filter(task => task.taskStatus !== "deleted")
        : this.tasks.filter(task => task.taskStatus === this.filterBy);

        task_1 = task_1.filter(el =>{
          return el.taskTitle.toLowerCase().includes(this.searchText.toLowerCase())
          // return true;
        })

        // return this.searchText.toLowerCase();
        // if (this.currentProject === 'all' ||this.currentProject === '' || this.currentProject === null){
        //   return task_1;
        // }
        // else {
        //   task_1 = task_1.filter(el =>{
        //     el.taskProject.trim() === this.currentProject;//.trim();
        //   })
        //   return task_1;
        // }

        task_1 = task_1.filter(el =>{
          if(this.currentProject === 'all'){
            return true;
          }
            return el.taskProject === this.currentProject;//.trim();
          })
        return task_1;
    },


    getCompletionPercentage(){
      // This I have changed so that it shows on the basis of project
      let total_sum = 0;
      let completed_sum = 0;

      let projectTasks = this.tasks.filter(el =>{
          if(this.currentProject === 'all'){
            return true;
          }
            return el.taskProject === this.currentProject;//.trim();
          })

      projectTasks.forEach(el => {
        if(el.taskStatus === true || el.taskStatus === false){
          total_sum += parseInt(el.taskPriority);
        }
        // total_sum += parseInt(el.taskPriority);
        if(el.taskStatus === true){
          completed_sum += parseInt(el.taskPriority);
        }
      });

      // return this.tasks;
      let done_percent = 100*completed_sum/total_sum
      return done_percent.toFixed(2);
    },
  }
};
</script>

<style scoped>
.blue {
  background-color: cornflowerblue;
}
  .filter-bar{
    display: flex;
    justify-content: space-around;
    padding: 3px 0;
}
</style>
