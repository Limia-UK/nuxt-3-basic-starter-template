<template>
  <v-app :theme="theme">
    <v-app-bar>
      <v-spacer>
        <div class="flex items-center">
          <!-- Logo placeholder, you can replace this with your actual logo -->
          <img src="/static/logo.png" alt="Logo" class="h-12 w-15" />
        </div>
      </v-spacer>

      <!-- Show the buttons centered on medium and larger screens -->
      <div class="hidden md:flex">
        <div class="mr-2" v-for="link in links">
          <v-btn
            :variant="isLinkActive(link.url) ? 'outlined' : 'text'"
            :rounded="isLinkActive(link.url) ? 'lg' : 'lg'"
            :disabled="isLinkActive(link.url)"
          >
            <nuxt-link :to="link.url">{{ link.name }}</nuxt-link>
          </v-btn>
        </div>
      </div>

      <!-- Dropdown Menu for mobile -->
      <div class="md:hidden mr-2">
        <v-row justify="center">
          <v-menu v-model="isMenuOpen" transition="scroll-y-transition">
            <template v-slot:activator="{ props }">
              <v-btn @click="toggleMenu" v-bind="props">
                <v-icon>
                  <template v-if="isMenuOpen"> mdi-close </template>
                  <template v-else> mdi-menu </template>
                </v-icon>
              </v-btn>
            </template>
            <v-list>
              <v-list-item v-for="link in links" :key="link.name" link>
                <nuxt-link :to="link.url">
                  <v-list-item-title v-text="link.name"></v-list-item-title>
                </nuxt-link>
              </v-list-item>
            </v-list>
          </v-menu>
        </v-row>
      </div>

      <div class="border-l ml-4 pl-2 pr-2">
        <v-btn
          :prepend-icon="
            theme === 'light' ? 'mdi-weather-sunny' : 'mdi-weather-night'
          "
          @click="onClick"
        >
        </v-btn>
      </div>
    </v-app-bar>

    <v-main>
      <v-container>
        <NuxtPage />
      </v-container>
    </v-main>

    <v-footer>
      <v-row justify="center" no-gutters>
        <div v-if="footerButtons">
          <v-btn
            v-for="link in links"
            :key="link.name"
            class="mx-2"
            :rounded="isLinkActive(link.url) ? 'sm' : ''"
            :disabled="isLinkActive(link.url)"
          >
            <nuxt-link :to="link.url">{{ link.name }}</nuxt-link>
          </v-btn>
        </div>
        <div v-else>
          <span
            class="p-4"
            v-for="link in links"
            :key="link.name"
            :class="{ 'non-clickable': isLinkActive(link.url) }"
          >
            <nuxt-link :to="link.url">{{ link.name }}</nuxt-link>
          </span>
        </div>
        <v-col class="text-center mt-4" cols="12">
          {{ new Date().getFullYear() }} — <strong>Company Name</strong>
        </v-col>
      </v-row>
    </v-footer>
  </v-app>
</template>

<style>
.non-clickable {
  pointer-events: none;
  opacity: 0.6; /* You can adjust the opacity to style the non-clickable links */
}
</style>

<script setup>
import { ref, onMounted } from "vue";
import { getData, setData } from "nuxt-storage/local-storage";
import { useRouter } from "vue-router";

const links = ref([
  { name: "Home", url: "/" },
  { name: "About", url: "/about" },
  { name: "Contact", url: "/contact" },
]);

const theme = ref();
const router = useRouter();
const footerButtons = ref(false); // Set it to true by default
const isMenuOpen = ref(false);

onMounted(() => {
  if (getData("theme") === null) {
    theme.value = "light";
  } else {
    theme.value = getData("theme");
  }
});

function onClick() {
  theme.value = theme.value === "light" ? "dark" : "light";
  setData("theme", theme.value);
}

function toggleMenu() {
  isMenuOpen.value = !isMenuOpen.value;
}

function isLinkActive(linkUrl) {
  return router.currentRoute.value.path === linkUrl;
}
</script>
