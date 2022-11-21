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
                      <q-input
                        v-model="apiname"
                        label="API Name"
                        @update:model-value="
                          (v) => {
                            update('name', v);
                          }
                        "
                      />
                    </div>
                    <div class="col">
                      <q-input
                        v-model="version"
                        label="version"
                        @update:model-value="
                          (v) => {
                            update('version', v);
                          }
                        "
                      />
                    </div>
                  </div>
                  <div class="row">
                    <div class="col">
                      <q-input
                        v-model="description"
                        label="description"
                        @update:model-value="
                          (v) => {
                            update('description', v);
                          }
                        "
                      />
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
                      <q-select
                        v-model="httpmethod"
                        :options="methods"
                        label="Method"
                      />
                    </div>
                    <div class="col">
                      <q-input v-model="resource" label="Resource Path" />
                    </div>
                  </div>
                  <div class="row">
                    <div class="col">
                      <q-input v-model="summary" label="summary" />
                    </div>
                  </div>
                  <div class="row">
                    <div class="col">
                      <div class="q-gutter-sm">
                        <q-input v-model="desc" label="description" />
                      </div>
                    </div>
                  </div>
                  <div class="row">
                    <div class="col">
                      <div class="q-gutter-sm">
                        <q-input
                          v-model="payload"
                          type="textarea"
                          label="payload"
                        />
                      </div>
                    </div>
                  </div>
                  <div class="row">
                    <div class="col">
                      <div class="q-gutter-sm">
                        <q-btn
                          color="primary"
                          label="add"
                          @click="() => addOperation()"
                        />
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
    var resource = ref("");
    var summary = ref("");
    var desc = ref("");
    const route = useRoute();
    const swagger = ref({});
    var httpmethod = ref(null);
    var payload = ref("");
    const swaggerUI = ref({});
    var methods = [
      "post",
      "GET",
      "PUT",
      "PATCH",
      "DELETE",
      "COPY",
      "HEAD",
      "OPTIONS",
    ];
    api
      .get("/apis/" + route.params.id)
      .then((response) => {
        console.log(response.data[0]);
        apiname.value = response.data[0].name;
        version.value = response.data[0].version;
        description.value = response.data[0].description;
        swagger.value = response.data[0].Swagger;

        const spec = swagger.value;
        var swaggerUI = SwaggerUI({
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
      httpmethod,
      methods,
      resource,
      summary,
      desc,
      swagger,
      swaggerUI,
      payload,
    };
  },
  mounted() {},
  methods: {
    update(k, v) {
      if (k == "name") {
        this.swagger.info.title = v;
      }
      if (k == "version") {
        this.swagger.info.version = v;
      }
      if (k == "description") {
        this.swagger.info.description = v;
      }
      this.swaggerUI = null;
      this.swaggerUI = SwaggerUI({
        spec: this.swagger,
        dom_id: "#swagger",
      });
    },
    addOperation() {
      var request = JSON.parse(this.payload);
      var nodes = Object.keys(request);
      //console.log(nodes);
      var attrs = []; // Array to store all the attributes
      console.log(nodes);
      for (let node in nodes) {
        // getting the attributes of current node.
        console.log(node);
        if (typeof request[nodes[node]] != "object") {
          console.log(typeof request[nodes[node]]);
        } else if (Array.isArray(request[nodes[node]])) {
          console.log("array");
        } else {
          console.log("object");
        }
      }
      this.swagger.paths = {};
      this.swagger.paths["/" + this.resource] = {};
      this.swagger.paths["/" + this.resource][this.httpmethod] = {};
      this.swagger.paths["/" + this.resource][this.httpmethod]["summary"] =
        this.summary;
      this.swaggerUI = null;
      this.swaggerUI = SwaggerUI({
        spec: this.swagger,
        dom_id: "#swagger",
      });
    },
  },
  data() {},
};
</script>
