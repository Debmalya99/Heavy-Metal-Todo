<template>
  <div>
    <h3></h3>


    <div class="accordion" id="accordionExample">
  <div class="accordion-item">
    <h2 class="accordion-header" id="headingOne">
      <button class="accordion-button" type="button" data-bs-toggle="collapse" data-bs-target="#collapseOne" aria-expanded="true" aria-controls="collapseOne" @click="showTaskForm = !showTaskForm">
        {{showTaskForm?'Add a new task':'Add a new task(Click to expand)'}}
      </button>
    </h2>
    <div id="collapseOne" :class="{'accordion-collapse':true, collapse:true, show:showTaskForm}" aria-labelledby="headingOne" data-bs-parent="#accordionExample">
      <div class="accordion-body">


        <form style="padding: 10px;">
  <div class="mb-3">
    <label for="exampleInputEmail1" class="form-label">Task Name</label>
    <input type="text" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp" placeholder="Let's be super productive today" v-model="task.newTaskTitle">
    
  </div>

  <div class="mb-3">
    <label for="exampleInputEmail1" class="form-label">Task Description</label>
    <input type="text" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp" placeholder="Let's be really specific about it" v-model="task.newTaskDesc">
    
  </div>
  
  <label class="form-label">Weightage</label>
      <select v-model="task.newTaskPriority" class="form-select">
        <option>1</option>
        <option>2</option>
        <option>3</option>
        <option>4</option>
        <option>5</option>
      </select>

  <label class="form-label">Project</label>
      <select v-model="task.projectName" class="form-select">
        <option v-for="proj in projectList" :key="proj">
          {{proj}}
        </option>
      </select>

  <button type="submit" class="btn btn-primary" @click="processNewTaskCreated(task)">Add Task</button>
</form>
      </div>
    </div>
  </div>
  
</div>


  </div>
</template>

<script>
import '../../node_modules/bootstrap/dist/css/bootstrap.min.css';


export default {
  name: "NewTask",
  components: "",
  props: {
    projectList:Array,
  },
  data() {
    return {
      task: {
        newTaskTitle: "",
        newTaskDesc: "",
        newTaskPriority:1,
        projectName:"all",
      },
      showTaskForm:false,
    };
  },
  methods: {
    processNewTaskCreated(task){
      this.$emit('create-new-task', task);
      // alert(this.task.projectName);
      this.task.newTaskTitle = "";
      this.task.newTaskDesc = "";
      this.newTaskPriority = 1;
      this.task.projectName = "all";
    }
  },
  computed: {}
};
</script>

<style></style>
