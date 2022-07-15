<template>
  <div class="max-w-screen-sm mx-auto px-4 py-10">
    <!-- App message -->
    <div v-if="statusMsg || errorMsg" class="mb-10 p-4 rounded-md shadown-md bg-light-grey">
      <p class="text-at-light-green">
        {{ statusMsg }}
      </p>
      <p class="text-red-500">
        {{ errorMsg }}
      </p>
    </div>

    <!-- Workout -->
    <div v-if="dataLoaded" class="max-w-screen-sm mx-auto">
      
      <!-- Workout general info -->
      <div class="flex flex-col items-center w-full mb-6 p-10 bg-light-grey rounded-md shadow-md relative">
        <!-- Controll buttons -->
        <div v-if="user" class="flex absolute gap-2 top-4 left-4">
          <div @click="editToggle" class="bg-at-light-green rounded-full p-2 cursor-pointer">
            <div><img src="@/assets/images/pencil-light.png" class="h-5" alt=""></div>
          </div>
          <div class="bg-at-light-green rounded-full p-2 cursor-pointer">
            <div><img src="@/assets/images/trash-light.png" class="h-5" alt=""></div>
          </div>
        </div>
        <!-- Cardio img -->
        <img v-if="data.workoutType === 'cardio'" src="@/assets/images/running-light-green.png" class="h-24 w-auto" alt="cardio">

        <!-- Strength Training img -->
        <img v-if="data.workoutType === 'strength'" src="@/assets/images/dumbbell-light-green.png" class="h-24 w-auto" alt="strength training">

        <p class="mt-6 py-1 px-3 text-xs text-white bg-at-light-green shadow-md rounded-lg">
          {{ data.workoutType }}
        </p>

        <input v-if="editMode" type="text" class="mt-8 mb-2 w-full p-2 focus:outline-none"  v-model="data.workoutName" />

        <h2 v-else class="mt-8 mb-2 text-center text-xl text-at-light-green">
          {{ data.workoutName }}
        </h2>
      </div>

      <!-- Exercises -->
      <div>
        <!-- Strength training -->
        <div v-if="data.workoutType === 'strength'" class="p-10 bg-light-grey rounded-md shadow-md">
          <div v-for="(exercise, index) in data.exercises" :key="index" class="flex flex-col gap-x-6 gap-y-2 mb-4 md:flex-row relative">
            <div class="flex flex-col md:w-1/3">
              <label for="exercise-name" class="mb-1 text-sm text-at-light-green">
                Exercise
              </label>
              <input v-if="editMode" type="text" class="w-full p-2 focus:outline-none" v-model="exercise.exercise" id="exercise-name"/>
              <p v-else>{{ exercise.exercise }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="reps" class="mb-1 text-sm text-at-light-green">
                Reps
              </label>
              
              <input v-if="editMode" type="text" required class="p-2 w-full text-grey-500 focus:outline-none" v-model="exercise.reps" id="reps">
              <p v-else>{{ exercise.reps }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="sets" class="mb-1 text-sm text-at-light-green">
                Sets
              </label>
              
              <input v-if="editMode" type="text" required class="p-2 w-full text-grey-500 focus:outline-none" v-model="exercise.sets" id="sets">
              <p v-else>{{ exercise.sets }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="weight" class="mb-1 text-sm text-at-light-green">
                Weight (LB's)
              </label>
              
              <input v-if="editMode" type="text" required class="p-2 w-full text-grey-500 focus:outline-none" v-model="exercise.weight" id="weight">
              <p v-else>{{ exercise.weight }}</p>
            </div>
            <img v-if="editMode" @click="deleteExercise(exercise.id)" src="@/assets/images/trash-light-green.png"  class="h-4 w-auto absolute -left-5 top-1 cursor-pointer" alt="" />
          </div>
          <button v-if="editMode" @click="addExercise" class="mt-6 py-2 px-6 rounded-sm self-start text-sm text-white bg-at-light-green duration-200 border-solid border-2 border-transparent hover:border-at-light-green hover:bg-white hover:text-at-light-green">Add Exercise</button>
        </div>

        <!-- Cardio -->
        <div v-if="data.workoutType === 'cardio'" class="p-10 bg-light-grey rounded-md shadow-md">
          <div v-for="(exercise, index) in data.exercises" :key="index" class="flex flex-col gap-x-6 gap-y-2 mb-4 md:flex-row relative">
            <div class="flex flex-col md:w-1/3">
              <label for="cardio-type" class="mb-1 text-sm text-at-light-green">
                Type
              </label>
              <select v-if="editMode" id="cardio-type" class="p-2 w-full text-grey-500 bg-white focus:outline-none" v-model="exercise.cardioType">
                <option value="#" selected>Select type</option>
                <option value="run">Run</option>
                <option value="walk">Walk</option>
              </select>
              <p v-else class="capitalize">{{ exercise.cardioType }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="distance" class="mb-1 text-sm text-at-light-green">
                Distance
              </label>
              
              <input v-if="editMode" type="text" required class="p-2 w-full text-grey-500 focus:outline-none" v-model="exercise.distance" id="distance">
              <p v-else>{{ exercise.distance }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="duration" class="mb-1 text-sm text-at-light-green">
                Duration
              </label>
              
              <input v-if="editMode" type="text" required class="p-2 w-full text-grey-500 focus:outline-none" v-model="exercise.duration" id="duration">
              <p v-else>{{ exercise.duration }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="pace" class="mb-1 text-sm text-at-light-green">
                Pace
              </label>
              
              <input v-if="editMode" type="text" required class="p-2 w-full text-grey-500 focus:outline-none" v-model="exercise.pace" id="pace">
              <p v-else>{{ exercise.pace }}</p>
            </div>
            <img v-if="editMode" @click="deleteExercise(exercise.id)" src="@/assets/images/trash-light-green.png"  class="h-4 w-auto absolute -left-5 top-1 cursor-pointer" alt="" />
          </div>
          <button v-if="editMode" @click="addExercise" class="mt-6 py-2 px-6 rounded-sm self-start text-sm text-white bg-at-light-green duration-200 border-solid border-2 border-transparent hover:border-at-light-green hover:bg-white hover:text-at-light-green">Add Exercise</button>
        </div>
      </div>

      <!-- Update -->
      <button v-if="editMode" @click="updateWorkout" class="mt-6 py-2 px-6 rounded-sm self-start text-sm text-white bg-at-light-green duration-200 border-solid border-2 border-transparent hover:border-at-light-green hover:bg-white hover:text-at-light-green">Update Workout</button>
    </div>

  </div>
</template>

<script>
import { ref, computed } from 'vue';
import { supabase } from '../supabase/init';
import { useRoute, useRouter } from 'vue-router';
import store from '../store/index';
import { uid } from 'uid';

export default {
  // name: "view-workout",
  setup() {
    // Create data / vars
    const data = ref(null)
    const dataLoaded = ref(null)
    const errorMsg = ref(null)
    const statusMsg = ref(null)
    const route = useRoute()
    const router = useRouter()
    const user = computed(() => store.state.user)

    // Get current Id of route
    const currentId = route.params.workoutId

    // Get workout data
    const getData = async () => {
      try {
        let { data: workout, error } = await supabase
        .from('workouts')
        .select('*')
        .eq('id', currentId)
        if (error) throw error
        data.value = workout[0]
        dataLoaded.value = true
      } catch(error) {
        errorMsg.value = error.message
        setTimeout(() => {
          errorMsg.value = false
        }, 5000)
      }
    }

    getData()

    // Delete workout
    const deleteWorkout = async () => {
      try {
        const { error } = await supabase
        .from('workouts')
        .delete()
        .eq('id', currentId)
        router.push({ name: 'Home' })
        if (error) throw error
      } catch(error) {
        errorMsg.value = `Error: ${error.message}`
        setTimeout(() => {
          errorMsg.value = false
        }, 5000);
      }
    }

    // Edit mode
    const editMode = ref(null)

    const editToggle = () => {
      editMode.value = !editMode.value
    }

    // Add exercise
    const addExercise = () => {
      if (data.value.workoutType === 'strength') {
        data.value.exercises.push({
          id: uid(),
          exercise: '',
          sets: '',
          reps: '',
          weight: '',
        })
        return
      }
      if (data.value.workoutType === 'cardio') {
        data.value.exercises.push({
          id: uid(),
          cardioType: '',
          distance: '',
          duration: '',
          pace: '',
        })
        return
      }
    }

    // Delete exercise
    const deleteExercise = (id) => {
      if (data.value.exercises.length > 1) {
        data.value.exercises = data.value.exercises.filter((exercise) => exercise.id !== id)
        return
      }
      errorMsg.value = 'Error: Cannot remove, need to have at least one exercise'
      setTimeout(() => {
        errorMsg.value = false
      }, 5000)
    }

    // Update Workout
    const updateWorkout = async () => {
      try {
        const { error } = await supabase.from('workouts').update([
          {
            workoutName: data.value.workoutName,
            workoutType: data.value.workoutType,
            exercises: data.value.exercises,
          }
        ]).eq('id', currentId)
        if (error) throw error
        editMode.value = false
        statusMsg.value = 'Success: Workout updated!'
        setTimeout(() => {
          statusMsg.value = false
        }, 5000)
      }
      catch(error) {
        errorMsg.value = `Error: ${error.message}`
        setTimeout(() => {
          errorMsg.value = false
        }, 5000)
      }
    }

    return { user, data, dataLoaded, errorMsg, statusMsg, editMode, editToggle, addExercise, deleteExercise, deleteWorkout, updateWorkout };
  },
};
</script>
