<script setup>

  import Navbar from './components/Navbar.vue';
  import Landing from './components/Landing.vue';
  import Projects from './components/Projects.vue';
  import Tools from './components/Tools.vue';
  import Contact from './components/Contact.vue';
  import Footer from './components/FooterComponent.vue';

</script>

<template>
  <body class="min-vh-100 d-flex flex-column custom-bg custom-text">
    <Navbar/>

    <div class="container flex-grow-1 mt-5 pt-4 mb-5 pb-5">
      <Landing/>
      <Projects/>
      <Tools/>
      <Contact/>

    </div>
      <Footer/>
  </body>
  

</template>
