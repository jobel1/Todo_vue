
<script setup>
import { ref,onMounted,watch } from 'vue';

const newTask=ref("");
const tasks=ref([]);
const lastUpdated=ref(" ");

onMounted(() => {
    const saved=localStorage.getItem("tasks");
    const savedTime=localStorage.getItem("lastUpdated");
    if(saved) {
        tasks.value=JSON.parse(saved)
    }
    if(savedTime) {
        lastUpdated.value=savedTime;
    }
});

 watch(tasks,(newTasks) => {
    localStorage.setItem("tasks",JSON.stringify(newTasks));
    if(newTasks.length ===0) {
        localStorage.removeItem("lastUpdated");
        lastUpdated.value=" ";
    }
 }, {deep:true});

 function formatDate() {
    const dates=new Date();
    return dates.toLocaleString();
 }

 function addTask() {
    if(newTask.value.trim()) {
        tasks.value.push({text: newTask.value.trim(),completed: false});
        newTask.value="";

        lastUpdated.value=formatDate();
        localStorage.setItem("lastUpdated", lastUpdated.value);
    }
    
 }

 function toggleTask(index) {
    tasks.value[index].completed = !tasks.value[index].completed;
 }

 function removeTask(index) {
    tasks.value.splice(index,1);
    if(tasks.value.length ===0) {
        lastUpdated.value=" ";
        localStorage.removeItem("lastUpdated");
    }
 }

</script>

<template>
  
    <div class="h-screen  bg-gradient-to-r from-slate-300 to-cyan-900">
        <div :class="themeClasses" class=" flex items-center justify-center text-center">

        <div class="w-full max-w-2xl px-4 ">
        <h1 class="pt-44 flex items-center justify-center text-bold text-white text-2xl mb-8">The Grand Scheme</h1>

             <div class=" flex justify-center items-center space-x-2 mb-6">
                <input v-model="newTask" @keyup.enter="addTask" type="text" placeholder="Enter a task" class="max-w-md flex-1 px-4 py-2 bg-black text-gray-200 placeholder-gray-400 rounded-l-full focus:outline-none"/>
                <button @click="addTask" class="bg-zinc-100 hover:bg-neutral-400 text-black font-semibold px-4 py-2 rounded-r-full transition -ml-3">Got it !
                </button>
                </div>
                <div v-if="lastUpdated" class="py-2 text-gray-300 text-sm mb-4">{{ lastUpdated }}

                </div>

                <transition-group name="list" tag="ul" class="space-y-3">
                    <li v-for="(task,index) in tasks":key="task.text + index" class="w-75 mx-auto flex justify-between items-center bg-gray-800 text-gray-200 px-4 py-1.5 rounded-3xl"><span :class="task.completed ? 'line-through text-gray-400' : ''" class="flex-1 text-left"> {{ task.text }}</span>
                    <button @click="toggleTask(index)" class="ml-2 w-8 h-8 flex items-center justify-center rounded-full border border-gray-400 hover:bg-green-500 transition" :class="task.completed ? 'bg-green-600 text-white' : 'bg-gray-700 text-gray-200'"> ✓ </button>
                        <button @click="removeTask(index)" class="pl-2 text-red-400 hover:text-red-600 transition">🗑</button>
                    </li>
                </transition-group>
         </div>     
    </div>
    </div>
</template>

<style>
.list-enter-active,
.list-leave-active {
    transition: all 0.4s ease;
}

.list-enter-from,
.list-leave-to {
    opacity:0;
    transform:translateY(-10px);
}

</style>