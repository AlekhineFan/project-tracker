<template>
  <div class="home">
    <FilterNav @filterChange="current = $event" :current="current" />
    <div v-if="projects.length">
      <div v-for="project in filteredProjects" :key="project.id">
        <SingleProject :project="project" @projectDeleted="handleDelete" @projectCompleted="handleComplete" />
      </div>
    </div>
  </div>
</template>

<script>
  import SingleProject from '../components/SingleProject';
  import FilterNav from '../components/FilterNav';

  export default {
    name: 'Home',
    components: { SingleProject, FilterNav },
    data() {
      return {
        projects: [],
        current: 'all',
      };
    },
    mounted() {
      fetch('http://localhost:3000/projects')
        .then(res => res.json())
        .then(data => (this.projects = data))
        .catch(err => console.log(err));
    },
    methods: {
      handleDelete(id) {
        this.projects = this.projects.filter(p => p.id != id);
      },
      handleComplete(id) {
        let p = this.projects.find(p => p.id === id);
        p.complete = !p.complete;
      },
    },
    computed: {
      filteredProjects() {
        if (this.current === 'completed') return this.projects.filter(p => p.complete);
        if (this.current === 'ongoing') return this.projects.filter(p => !p.complete);

        return this.projects;
      },
    },
  };
</script>
