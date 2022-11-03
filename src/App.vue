<template>
  <v-app id="inspire">
    <v-app-bar app>
      <v-toolbar-title>Text recognition with Tesseract js library</v-toolbar-title>
    </v-app-bar>
    <v-main>
      <v-container class="grey lighten-5">
        <TesseractApp />
      </v-container>
    </v-main>
    <Footer />
  </v-app>
</template>

<script>

import Footer from './components/Footer.vue';
import TesseractApp from './components/TesseractApp.vue';

export default {
  components: { Footer, TesseractApp }
}

</script>