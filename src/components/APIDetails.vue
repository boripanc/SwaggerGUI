<template>
  <div class="row">
    <div class="col">
      <div class="q-pa-md items-start q-gutter-md">
        <div class="row">
          <div class="col">
            <q-card class="my-card bg-grey-1 text-black">
              <q-card-section>
                <div class="text-h6">Basic Info</div>
              </q-card-section>
              <q-card-section
                ><div class="q-gutter-md" style="max-width: 600px">
                  <div class="row">
                    <div class="col">
                      <q-input v-model="apiname" label="API Name" />
                    </div>
                    <div class="col">
                      <q-input v-model="version" label="version" />
                    </div>
                  </div>
                  <div class="row">
                    <div class="col">
                      <q-input v-model="description" label="description" />
                    </div>
                    <div class="col">
                      <div class="q-gutter-sm">
                        <q-checkbox dense v-model="http" label="http" />
                        <q-checkbox dense v-model="https" label="https" />
                      </div>
                    </div>
                  </div>
                </div>
              </q-card-section>
            </q-card>
          </div>
        </div>
        <div class="row">
          <div class="col">
            <q-card class="my-card bg-grey-1 text-black">
              <q-card-section>
                <div class="text-h6">Operation Info</div>
              </q-card-section>
              <q-card-section
                ><div class="q-gutter-md" style="max-width: 600px">
                  <div class="row">
                    <div class="col">
                      <q-input v-model="apiname" label="API Name" />
                    </div>
                    <div class="col">
                      <q-input v-model="version" label="version" />
                    </div>
                  </div>
                  <div class="row">
                    <div class="col">
                      <q-input v-model="description" label="description" />
                    </div>
                    <div class="col">
                      <div class="q-gutter-sm">
                        <q-checkbox dense v-model="http" label="http" />
                        <q-checkbox dense v-model="https" label="https" />
                      </div>
                    </div>
                  </div>
                </div>
              </q-card-section>
            </q-card>
          </div>
        </div>
      </div>
    </div>
    <div class="col"><div class="swagger" id="swagger"></div></div>
  </div>
</template>
<style>
.swagger-ui .info .title small {
  background: #7d8492;
  border-radius: 57px;
  display: inline-block;
  font-size: 10px;
  margin: 0 0 0 5px;
  padding: 2px 4px;
  position: relative;
  top: -5px;
  vertical-align: super;
  line-height: 1;
}
</style>
<script>
import { api } from "src/boot/axios";
import { useRoute } from "vue-router";
import SwaggerUI from "swagger-ui";
import "swagger-ui/dist/swagger-ui.css";

import { ref } from "vue";
export default {
  setup() {
    var address = ref("");
    var apiname = ref("");
    var version = ref("");
    var description = ref("");
    const route = useRoute();
    var swagger = ref({});
    api
      .get("/apis/" + route.params.id)
      .then((response) => {
        console.log(response.data[0]);
        apiname.value = response.data[0].name;
        version.value = response.data[0].version;
        description.value = response.data[0].description;
        swagger.value = response.data[0].Swagger;

        const spec = swagger.value;
        SwaggerUI({
          spec: spec,
          dom_id: "#swagger",
        });
      })
      .finally(() => {});
    function saveELEMENT() {
      console.log(address);
    }
    return {
      apiname,
      version,
      description,
      saveELEMENT,
    };
  },
  mounted() {},
  methods: {},
  data() {},
};
</script>
