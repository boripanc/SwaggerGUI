<template><APIDetails></APIDetails></template>

<script>
import APIDetails from "src/components/APIDetails.vue";
import { defineComponent } from "vue";

export default defineComponent({
  name: "APIsPage",
  components: { APIDetails },
});
</script>
