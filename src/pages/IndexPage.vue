<template>
  <APITableList></APITableList>
</template>

<script>
import { defineComponent } from "vue";
import APITableList from "src/components/APITableList.vue";

export default defineComponent({
  name: "IndexPage",
  components: { APITableList },
});
</script>
