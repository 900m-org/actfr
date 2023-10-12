<template>
    <nav class="navbar navbar-expand-lg  navbar-full-width">
  <div class="container-fluid">
    <a class="navbar-brand " href="#"><img src="../public/photos/logo-900M-carre.jpg" alt="logo" style="max-width: 200px; max-height: auto;  ; margin-left: 20px;"></a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav nav-fill w-100">
        <li class="nav-item">
          <a class="nav-link fs-5" aria-current="page" href="#" style="text-decoration: none  !important; color: black; ">Nous découvrir</a>
        </li>
        <li class="nav-item">
          <a class="nav-link fs-5 "   style="text-decoration: none  !important; color: black;">Se former</a>
        </li>
        <li class="nav-item">
          <a class="nav-link fs-5" style="text-decoration: none  !important; color: black; " href="#">Blog</a>
        </li>
        <li class="nav-item">
          <a class="nav-link disabled" style="text-decoration: none  !important;"><div style="width: 120px; height: 40px; background-color: rgb(221, 236, 203); display: flex; justify-content: center; align-items: center; color: black; font-weight: bold;  border-radius: 20px;">S'engager</div></a>
        </li>
        <li class="nav-item d-flex">
          <a class="nav-link disabled me-2" style="text-decoration: none  !important;"><div style="width: 120px; height: 40px;background-color: rgb(49, 104, 54); display: flex; justify-content: center; align-items: center; color: white; font-weight: bold; border-radius: 20px;" >Don</div></a>
        </li>
      </ul>
      
    </div>
  </div>
</nav>
</template>

<style>

  /* Add the following styles to make the navbar take full width */
  .navbar-full-width {
    width: 100%;
  }

  .container-fluid {
    padding-left: 0;
    padding-right: 0;
  }

  /* Make the nav-items occupy full width evenly */
  .navbar-full-width .navbar-nav {
    display: flex;
    justify-content: space-around;
    padding: 0; /* Remove padding between nav-items */
  }

</style>