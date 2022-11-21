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
                          @update:model-value="
                            (v) => {
                              this.value = prettyprint(v);
                            }
                          "
                        />
                      </div>
                    </div>
                  </div>
                  <div class="row">
                    <div class="col">
                      <div class="q-gutter-sm">
                        <q-input
                          v-model="response"
                          type="textarea"
                          label="response"
                          @update:model-value="
                            (v) => {
                              this.value = prettyprintResponse(v);
                            }
                          "
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
    var response = ref("");
    const swaggerUI = ref({});
    const refRequest = ref({});
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
      refRequest,
      response,
      route,
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
    prettyprint(v) {
      this.payload = JSON.stringify(JSON.parse(v), null, 2);
    },
    prettyprintResponse(v) {
      this.response = JSON.stringify(JSON.parse(v), null, 2);
    },
    generateSchemas(payloadObj, type) {
      var schema = {};
      schema[this.resource + type] = {};
      schema[this.resource + type]["type"] = "object";
      var props = {};
      var request = JSON.parse(payloadObj);
      var nodes = Object.keys(request);
      for (let node in nodes) {
        var typ = "";
        props[nodes[node]] = {};
        if (typeof request[nodes[node]] != "object") {
          typ = typeof request[nodes[node]];
          if (typ == "number") typ == "integer";
          props[nodes[node]]["type"] = typ;
          props[nodes[node]]["example"] = request[nodes[node]];
        } else if (Array.isArray(request[nodes[node]])) {
          typ = "array";
          props[nodes[node]]["type"] = typ;

          if (typeof request[nodes[node]][0] == "object") {
            props[nodes[node]]["items"] = {
              type: "object",
              properties: this.getObjectChild(request[nodes[node]][0]),
            };
          } else {
            props[nodes[node]]["items"] = {
              type: typeof request[nodes[node]][0],
              example: request[nodes[node]][0],
            };
          }
        } else {
          typ = "object";
          props[nodes[node]]["type"] = typ;
          props[nodes[node]]["properties"] = this.getObjectChild(
            request[nodes[node]]
          );
        }
      }
      console.log(props);
      schema[this.resource + type]["properties"] = JSON.parse(
        JSON.stringify(props)
      );
      return schema;
    },
    getObjectChild(request) {
      var props = {};
      var nodes = Object.keys(request);
      for (let node in nodes) {
        var typ = "";
        props[nodes[node]] = {};
        if (typeof request[nodes[node]] != "object") {
          typ = typeof request[nodes[node]];
          if (typ == "number") typ == "integer";
          props[nodes[node]]["type"] = typ;
          props[nodes[node]]["example"] = request[nodes[node]];
        } else if (Array.isArray(request[nodes[node]])) {
          typ = "array";
          props[nodes[node]]["type"] = typ;

          if (typeof request[nodes[node]][0] == "object") {
            props[nodes[node]]["items"] = {
              type: "object",
              properties: this.getObjectChild(request[nodes[node]][0]),
            };
          } else {
            props[nodes[node]]["items"] = {
              type: typeof request[nodes[node]][0],
              example: request[nodes[node]][0],
            };
          }
        } else {
          typ = "object";
          props[nodes[node]]["type"] = typ;
          props[nodes[node]]["properties"] = this.getObjectChild(
            request[nodes[node]]
          );
        }
      }
      return props;
    },
    addOperation() {
      var request = JSON.parse(this.payload);
      var nodes = Object.keys(request);

      var requestBody = this.generateSchemas(this.payload, "_request");
      var responseBody = this.generateSchemas(this.response, "_response");
      this.swagger.paths = {};
      this.swagger.paths["/" + this.resource] = {};
      this.swagger.paths["/" + this.resource][this.httpmethod] = {};
      this.swagger.paths["/" + this.resource][this.httpmethod]["summary"] =
        this.summary;
      this.swagger.paths["/" + this.resource][this.httpmethod]["requestBody"] =
        {
          content: {
            "application/json": {
              schema: {
                $ref: "#/components/schemas/" + this.resource + "_request",
              },
            },
          },
        };
      this.swagger.paths["/" + this.resource][this.httpmethod]["responses"] = {
        200: {
          content: {
            "application/json": {
              schema: {
                $ref: "#/components/schemas/" + this.resource + "_response",
              },
            },
          },
        },
      };
      this.swagger["components"] = {};
      this.swagger["components"]["schemas"] = {};
      this.swagger["components"]["schemas"][this.resource + "_request"] =
        requestBody[this.resource + "_request"];
      this.swagger["components"]["schemas"][this.resource + "_response"] =
        responseBody[this.resource + "_response"];
      console.log(JSON.stringify(this.swagger));
      this.swaggerUI = null;
      this.swaggerUI = SwaggerUI({
        spec: this.swagger,
        dom_id: "#swagger",
      });
      api
        .post("/apis/" + this.route.params.id + "/operation", {
          HTTPMethod: this.httpmethod,
          Resource: this.resource,
          Parameters: this.parameters,
          RequestBody: this.payload,
          ResponseBody: this.response,
        })
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
    },
  },
  data() {},
};
</script>
