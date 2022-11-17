<template>
  <div class="q-pa-md">
    <q-table
      ref="tableRef"
      title="Treats"
      :rows="rows"
      :columns="columns"
      row-key="id"
      v-model:pagination="pagination"
      :loading="loading"
      :filter="filter"
      binary-state-sort
      @request="onRequest"
      ><template v-slot:body-cell-Name="props">
        <q-td :props="props">
          <a :href="'/apis/' + props.row._id">{{ props.row.name }}</a>
        </q-td>
      </template>
    </q-table>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import { api } from "boot/axios";
const columns = [
  {
    name: "Name",
    required: true,
    label: "API Name",
    align: "left",
    field: "name",
    sortable: true,
  },
  {
    name: "version",
    align: "center",
    label: "version",
    field: "version",
    sortable: true,
  },
];

export default {
  setup() {
    const tableRef = ref();
    const rows = ref([]);
    const filter = ref("");
    const loading = ref(false);
    const pagination = ref({
      sortBy: "desc",
      descending: false,
      page: 1,
      rowsPerPage: 3,
      rowsNumber: 10,
    });
    const fetchDogs = (page = 0) => {
      // we can specify the "page" to jump to
      loading.value = true;
      return api
        .get("/apis", {
          params: { page: page }, // here, we tell the api what "page" to jump to
        })
        .then((response) => {
          console.log(response.data);
          rows.value = response.data.result;
          // The api gives us "meta data".
          // this meta data gives us everything we
          // need to get pagination working!
          const meta = response.data.pageinfo;

          // We now update "pagination" with our meta data
          // from the server. This is where the magic happens 🪄
          pagination.value.page = meta.currentpage;
          pagination.value.rowsPerPage = meta.pagesize;
          pagination.value.rowsNumber = meta.totalrecord;
        })
        .finally(() => {
          loading.value = false;
        });
    };

    // QTable exposes a @request method (see the <template>)
    // This will be called when one of the pagination buttons are clicked.
    // it gives us everything we need, including the new page number!
    const onRequest = (props) => {
      fetchDogs(props.pagination.page);
    };
    // The initial fetch
    fetchDogs();
    return {
      tableRef,
      filter,
      loading,
      pagination,
      columns,
      rows,

      onRequest,
    };
  },
};
</script>
