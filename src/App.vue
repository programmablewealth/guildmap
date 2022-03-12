<script setup>
import { RouterView } from 'vue-router'
</script>

<template>
  <RouterView />
</template>

<style>
  /* Use simpler box-sizing reset, because the inherit version causes performance issues with large svg */
  *, *:before, *:after {
    box-sizing: border-box;
  }

  html, body, #app {
    height: 100%;
    margin: 0;
    padding: 0;
  }

  :root {
    --site-background-color: white;
    --site-text-color--subtle: #767676;
  }

  body {
    margin: 0;
    padding: 0;
    text-size-adjust: none;
    font-size: 16px;
    font-family: 'Liberation Sans', 'Helvetica Neue', 'Arial Nova', sans-serif;
  }

</style>
