<template>
  <v-container>
    <v-row>
      <v-spacer></v-spacer>
      <v-col cols="10">
        <h2>Tools developed by the R epidemics consortium (RECON) are essential in helping professionals design outbreak analytics pipelines in response to COVID19.</h2>
      </v-col>
      <v-spacer></v-spacer>
    </v-row>
    <v-row v-for="(fact, i) in facts" :key="i" align="center">
      <v-col v-if="i % 2 === 0" cols="4" align="center">
        <v-img :src="fact.img" class="my-3" contain height="200"/>
      </v-col>
      <v-col cols="8">
        <p>{{fact.txt}}</p>
      </v-col>
      <v-col v-if="i % 2 !== 0" cols="4">
        <v-img :src="fact.img" class="my-3" contain height="200"/>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "QuickFacts",

  data() {
    return {
      facts: [
        {
          txt:
            "The tasking manager is a direct communication channel between field agents actively working on the response to COVID19 and the R community.",
          img: require("../assets/Placeholder.png")
        },
        {
          txt:
            "Your R programming skills can help field agents around the world.",
          img: require("../assets/Placeholder.png")
        },
        {
          txt:
            "RECON packages have been used in many outbreaks such as the Ebola outbreaks in West Africa (2014-2016) and Eastern Democratic Republic of the Congo (2018-2020), and are currently used by various public health institutions and academic modelling groups in the COVID-19 response.",
          img: require("../assets/Placeholder.png")
        }
      ]
    };
  }
};
</script>
