<template>
  <v-app class="bg-grey-darken-4">
    <v-toolbar class="pa-1 pl-4 pr-4" color="amber-accent-3" app>
      <v-toolbar-title class="text-grey-darken-4 text-headline-small">Tasques</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn to="/newNote" icon variant="plain">
        <v-icon class="text-grey-darken-4">mdi-plus</v-icon>
      </v-btn>
    </v-toolbar>

    <v-main>
      <router-view />
    </v-main>
  </v-app>
</template>

<script setup>
</script>
