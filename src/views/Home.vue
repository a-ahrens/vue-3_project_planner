<template>
  <div class="home">
    <!-- We can use $event to access data directly from the emitted component -->
    <!-- In this instance, FilterNav sends back a string based on the clicked button -->
    <FilterNav @filterChange="current = $event" :current="current"/>
    <div v-if="projects.length">
      <div v-for="project in filterProjects" :key="project.id">
        <SingleProject :project="project" 
          @complete="handleComplete"
          @deleted="handleDelete"/>
      </div>
    </div>
  </div>
</template>

<script>
import FilterNav from '@/components/FilterNav.vue'
import SingleProject from '../components/SingleProject.vue'


export default {
  name: 'Home',
  components: { FilterNav, SingleProject },
  data() {
    return {
      projects: [],
      current: 'all'
    }
  },
  mounted() {
    fetch('http://localhost:3000/projects')
      .then(res => res.json())
      .then(data => this.projects = data)
      .catch(err => console.log(err.message))
  },
  methods: {
    handleComplete(id) {
      let p = this.projects.find(project => {
        return project.id === id
      })

      p.complete = !p.complete
    },

    handleDelete(id) {
      this.projects = this.projects.filter((project) => {
        return project.id !== id
      })
    }
  },
  computed: {
    filterProjects() {
      if(this.current === 'completed'){
        return this.projects.filter((project) => project.complete)
      } 
      if(this.current === 'ongoing'){
        return this.projects.filter((project) => !project.complete)
      }
      return this.projects
    }
  }
}
</script>
