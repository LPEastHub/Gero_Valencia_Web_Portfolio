<template>
    <div class="col-12 col-md-6 col-lg-4">
        <div class="bg-surface-dim p-1 beveled-out d-flex flex-column h-100">
            
            <div class="bg-primary-custom text-white px-2 py-1 text-xs fw-bold d-flex justify-content-between">
                <span>{{ project.title }}</span>
                <span>X</span>
            </div>
            
            <div class="bg-white p-1 beveled-in m-1 flex-shrink-0 overflow-hidden project-img-container">
                <img class="w-100 h-100 object-fit-cover" :alt="project.title" :src="project.image"/>
            </div>
            
            <div class="p-2 d-flex flex-column flex-grow-1 justify-content-between gap-2">
                <p class="project-desc">{{ project.description }}</p>
                <button class="retro-btn w-100 px-3 py-1 text-xs fw-bold mt-auto">OPEN_LINK.EXE</button>
            </div>

        </div>
    </div>
</template>

<script setup>
import { defineProps } from 'vue';

defineProps({
    project: {
        type: Object,
        required: true
    }
});
</script>