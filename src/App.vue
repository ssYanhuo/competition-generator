<template>
  <v-app>
    <v-main>
      <Generator/>
    </v-main>
  </v-app>
</template>

<script>
import Generator from './components/Generator';

export default {
  name: 'App',

  components: {
    Generator,
  },

  data: () => ({
    //
  }),
};
</script>
