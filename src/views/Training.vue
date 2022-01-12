<template>
  <div>
    <v-container fluid class="pa-1">
    <v-row dense>
      <v-col cols="12" md="4">
      <v-card class="px-4 pb-4 fill-height" elevation="1">
          <v-card-title>
            己选择的 Feature
          </v-card-title>
          <v-card-text>
            <v-autocomplete v-model="wantedFeature" :items="featureList"
              label="包含这些 feature" dense small-chips multiple outlined></v-autocomplete>
          </v-card-text>
          <v-card-action>
          </v-card-action>
        </v-card>
      </v-col>
       <v-col cols="12" md="4">
      <v-card class="px-4 pb-4 fill-height" elevation="1">
          <v-card-title>
            训练目标 Label
          </v-card-title>
          <v-card-text>
            <v-autocomplete v-model="wantedLabel" :items="labelList"
              label="包含这些 label" dense small-chips multiple outlined></v-autocomplete>
          </v-card-text>
          <v-card-action>
          </v-card-action>
        </v-card>
      </v-col>
    
      <v-col cols="12" md="4">
      <v-card class="px-4 pb-4 fill-height" elevation="1">
          <v-card-title>
            BinaryRelevance Kernel
          </v-card-title>
          <v-card-text>
            <v-radio-group v-model="classifierChoice" row>
              <v-radio v-for="item in classifierList" :key="item.value" :label="`${item.name}`" :value="item.value"></v-radio>
            </v-radio-group>
          </v-card-text>
          <v-card-action>
            <v-btn color="primary" @click="trainClassifier">train</v-btn>
          </v-card-action>
        </v-card>
      </v-col>
    </v-row>
      <v-row dense>
        <v-col cols="12">
        <v-card class="px-4 pb-4 fill-height" elevation="1">
        <v-data-table :headers="status.headers" :items="status.items" :items-per-page="10">
        </v-data-table>
        </v-card>
        </v-col>
    </v-row>
    </v-container>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Training',
  data: () => ({
      featureList: [],
      labelList: [],
      wantedFeature: [],
      wantedLabel: [],
      classifierList: [{name: "KNearestNeighbor", value: "knn"},{name: "SVM", value: "svc"}],
      classifierChoice: "knn",
      status: {
        headers: [{text: "BR Kernel", value: "brKernel"},
        {text: "Hamming Loss", value: "hammingLoss"},
        {text: "Hamming Score", value: "hammingScore"},
        {text: "Subset Accuracy", value: "accuracy"},
        {text: "Used Feature", value: "usedFeature"},
        {text: "Target Labels", value: "targetLabels"}
        ],
        items: []
      },
      
  }),
  mounted() {
      this.fetchdata();
  },
  methods: {
    fetchdata() {
      axios({
        method: "get",
        url: `/available/labels/${this.$store.state.dataset}`,
        baseURL: this.$store.state.helper.apiAddr
      }).then(res => res.data)
      .then(x => {
          this.labelList = x.labels;
      })
      axios({
        method: "get",
        url: `/available/features/${this.$store.state.dataset}`,
        baseURL: this.$store.state.helper.apiAddr
      }).then(res => res.data)
      .then(x => {
          this.featureList = x.features;
      })
    },
    trainClassifier() {
      axios({
        method: "post",
        url: `/train/classifier`,
        baseURL: this.$store.state.helper.apiAddr,
        headers: {"Content-Type": "application/json;charset=UTF-8"},
        data: { dataset_name: this.$store.state.dataset,
        select_feature: this.wantedFeature,
        select_label: this.wantedLabel, 
        classifier: "BinaryRelevance",
        br_kernel: this.classifierChoice}
      }).then(res => res.data)
      .then(x => {
        this.status.items.push({
          brKernel: x.br_kernel,
          hammingLoss: Number.parseFloat(x.hamming_loss).toPrecision(2),
          accuracy: Number.parseFloat(x.accuracy_score).toPrecision(2),
          hammingScore: Number.parseFloat(x.hamming_score).toPrecision(2),
          usedFeature: x.select_feature.length,
          targetLabels: x.select_label
        })
      })
    }
},
  computed: {
  }
}
</script>

<style scoped>
</style>