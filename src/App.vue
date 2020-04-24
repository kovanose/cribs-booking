<template>
  <div id="app">
    <div class="container">
      <Header />
      <router-view />
      <Footer />
    </div>
  </div>
</template>
<script>
import Header from "@/components/layout/AppHeader";
import Footer from "@/components/layout/AppFooter";
export default {
  name: "App",
  components: {
    Header,
    Footer
  }
};
</script>