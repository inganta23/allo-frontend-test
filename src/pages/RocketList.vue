<template>
  <v-container>
    <v-row align="center" class="mb-4">
      <v-col cols="12" md="8">
        <h1 class="text-h4 font-weight-bold">SpaceX Rockets</h1>
      </v-col>
      <v-col cols="12" md="4" class="text-md-right">
        <v-btn color="primary" prepend-icon="mdi-plus" @click="handleAddMock">
          Add Rocket
        </v-btn>
      </v-col>
    </v-row>

    <v-text-field
      v-model="filter"
      label="Search Rockets"
      prepend-inner-icon="mdi-magnify"
      variant="outlined"
      hide-details
      class="mb-6"
    ></v-text-field>

    <v-row v-if="store.loading" justify="center" class="my-12">
      <v-progress-circular
        indeterminate
        color="primary"
        size="64"
      ></v-progress-circular>
    </v-row>

    <v-alert
      v-else-if="store.error"
      type="error"
      title="Error"
      :text="store.error"
      class="mb-6"
    >
      <template v-slot:append>
        <v-btn variant="outlined" @click="store.loadData">Retry</v-btn>
      </template>
    </v-alert>

    <v-row v-else>
      <v-col
        v-for="rocket in filteredRockets"
        :key="rocket.id"
        cols="12"
        sm="6"
        md="4"
      >
        <RocketCard :rocket="rocket" />
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from "vue";
import { useRocketStore } from "@/stores/rocketStore";
import RocketCard from "@/components/RocketCard.vue";
import type { Rocket } from "@/types/rocket";

const store = useRocketStore();
const filter = ref("");

onMounted(() => {
  if (store.rockets.length === 0) store.loadData();
});

const filteredRockets = computed(() => {
  return store.rockets.filter((r) =>
    r.name.toLowerCase().includes(filter.value.toLowerCase()),
  );
});

const handleAddMock = () => {
  const newRocket: Rocket = {
    id: Date.now().toString(),
    name: "Allo-Vuetify Explorer",
    description: "A custom rocket added to demonstrate local state management.",
    flickr_images: [
      "https://upload.wikimedia.org/wikipedia/commons/9/9a/Soyuz_TMA-9_launch.jpg",
    ],
    cost_per_launch: 50000000,
    country: "Indonesia",
    first_flight: new Date().toISOString().split("T")[0],
  };
  store.addRocket(newRocket);
};
</script>
