<template>
  <div class="row">
    <div class="col">
      <div class="q-pa-md">
        <div class="q-gutter-md" style="max-width: 600px">
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
                <q-btn label="open dialog" @click="showDialog = !showDialog" />
                <q-dialog
                  v-model="customDialogModel"
                  stack-buttons
                  prevent-close
                  @ok="onOk"
                  @cancel="onCancel"
                  @show="onShow"
                  @hide="onHide"
                >
                  <!-- This or use "title" prop on <q-dialog> -->
                  <template v-slot:title>Favorite Superhero</template>

                  <!-- This or use "message" prop on <q-dialog> -->
                  <template v-slot:message
                    >What is your superhero of choice?</template
                  >

                  <template v-slot:body>
                    <q-field
                      icon="account_circle"
                      helper="We need your name so we can send you to the movies."
                      label="Your name"
                      :label-width="3"
                    >
                      <q-input v-model="name" />
                    </q-field>
                  </template>

                  <template v-slot:buttons="props">
                    <q-btn
                      color="primary"
                      label="Choose Superman"
                      @click="choose(props.ok, 'Superman')"
                    />
                    <q-btn
                      color="black"
                      label="Choose Batman"
                      @click="choose(props.ok, 'Batman')"
                    />
                    <q-btn
                      color="negative"
                      label="Choose Spiderman"
                      @click="choose(props.ok, 'Spiderman')"
                    />
                    <q-btn flat label="No thanks" @click="props.cancel" />
                  </template>
                </q-dialog>
              </div>
            </div>
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
  methods: {
    displaydata: function (props) {
      console.log(JSON.stringify(this.form));
      console.log(props);
    },
    onOk(data) {},

    // when props.cancel() gets called
    onCancel() {},

    // when we show it to the user
    onShow() {},

    // when it gets hidden
    onHide() {},

    // custom handler
    async choose(okFn, hero) {
      if (this.name.length === 0) {
        this.$q.dialog({
          title: "Please specify your name!",
          message: `Can't buy tickets without knowing your name.`,
        });
      } else {
        await okFn();
        this.$q.notify(`Ok ${this.name}, going with ${hero}`);
      }
    },
  },
  data() {
    return {
      customDialogModel: false,
      form: {
        username: null,
        email: null,
        password: null,
        confirmpassword: null,
      },
    };
  },
};
</script>
