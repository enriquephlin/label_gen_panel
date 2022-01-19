<template>
  <div>
    <v-container fluid class="pa-1">
    <v-row dense>
      <v-col cols="8">
      <v-autocomplete v-model="wantedLabel" :items="labelList"
          label="选择 Label" dense small-chips multiple outlined></v-autocomplete>
      </v-col>
      <v-col cols="4">
          <v-btn @click="debug"> debug </v-btn>
      </v-col>
       <v-row dense>
         <v-col cols="12">
          <v-data-table
          :headers="headers"
          :items="queryResult"
          :items-per-page="5"
          class="elevation-1"
          ></v-data-table>
         </v-col>
       </v-row>
    </v-row>
    </v-container>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Query',
  data: () => ({
      labelList: [],
      wantedLabel: [],
      headers: [{
        text: "id",
        value: "index",
      },{
        text: "feature",
        value: "feature"
      },{
        text: "label",
        value: "label"
      }],
      queryResult: [],
  }),
  mounted() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      axios({
        method: "get",
        url: `/available/labels/${this.$store.state.dataset}`,
        baseURL: this.$store.state.helper.apiAddr
      }).then(res => res.data)
      .then(x => {
          this.labelList = x.labels;
      })
    },
    debug() {
      console.log(this.wantedLabel)
      axios({
        method: "post",
        url: `/feature/query_label`,
        baseURL: this.$store.state.helper.apiAddr,
        headers: {"Content-Type": "application/json;charset=UTF-8"},
        data: { dataset_name: this.$store.state.dataset, dataset_type: "train",
        select_label: this.wantedLabel }
      }).then(res => res.data )
      .then(x => {
        let z=[]
        for (let key in x.feature_list){
          z.push({"index": key, "feature": x.feature_list[key], "label": x.label_list[key]})
        }
        this.queryResult = z;
        console.log(this.queryResult)
      })
    }
  },
  computed: {
  }
}
</script>

<style scoped>
</style>