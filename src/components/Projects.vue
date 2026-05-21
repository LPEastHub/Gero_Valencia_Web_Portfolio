<template>
    <section id="projects" class="mb-5">
        <div class="d-flex align-items-center gap-3 mb-4">
            <h2 class="font-headline fs-2 fw-bolder text-primary-custom mb-0">MY PROJECTS</h2>
            <div class="flex-grow-1 bg-surface-dim beveled-in h-4px"></div>
        </div>

        <div class="row g-4">
            <ProjectCard 
                v-for="project in projects" 
                :key="project.id" 
                :project="project" 
            />
        </div>
    </section>
</template>

<script setup>
import ProjectCard from './ProjectCard.vue';
import projects from '../data/projects.json';
</script>