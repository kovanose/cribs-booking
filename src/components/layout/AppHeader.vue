<template>
  <div class="row app-header">
    <nav class="navbar">
      <a class="navbar-brand" href="#">
        <img alt="Vue logo" src="@/assets/header_logo.png" />
      </a>
      <ul class="nav justify-content-end">
        <li class="nav-item">
          <a class="nav-link" href="#">
            <router-link to="/">HOME</router-link>
          </a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#">
            <router-link to="/booking">BOOK A VIEWING</router-link>
          </a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#">
            <router-link to="/contact">CONTACT US</router-link>
          </a>
        </li>
      </ul>
    </nav>
  </div>
</template>
<script>
export default {
  name: "AppHeader"
};
</script>
<style>
.app-header {
  display: block;
}
.app-header .navbar {
  background-color: #fff;
}
.app-header a {
  font-size: 14px;
  color: black;
}
.app-header a:hover {
  color: black;
}
</style>