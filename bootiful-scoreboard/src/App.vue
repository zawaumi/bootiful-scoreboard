<template>
  <div class="container">
    <div class="w-100 row m-0">
      <h1 class="col-md-12 text-center">ランキング</h1>
      <!--1~9位まで-->
      <div class="d-flex flex-column align-items-center col-6 p-5">
        <div v-for="index in 9" class="w-100 m-2 border border-1 rounded shadow">
          <h3>{{ index }} 位</h3>
          <div class="d-flex flex-row justify-content-center"><h5 class="flex-grow-1 text-end">{{ teams[index - 1].name }}</h5><h5 class="px-2">|</h5><h5 class="flex-grow-1 text-start">score: {{ teams[index - 1].score }}</h5></div>
          <h5> </h5>
        </div>
      </div>
      <!--10位以降-->
      <div class="d-flex flex-column align-items-center col-6 p-5">
        <div v-for="(team, index) in teams">
          <div v-if="9 <= index">
            {{ index + 1 }} 位{{ team.name }}score: {{ team.score }}</div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      teams: [
        {
          name: "test 1",
          score: "2",
        },
        {
          name: "test 2",
          score: "4",
        },
        {
          name: "test 3",
          score: "5",
        },
        {
          name: "test 4",
          score: "5",
        },
        {
          name: "test 5",
          score: "5",
        },
        {
          name: "test 6",
          score: "5",
        },
        {
          name: "test 7",
          score: "5",
        },
        {
          name: "test 8",
          score: "5",
        },
        {
          name: "test 9",
          score: "5",
        },
        {
          name: "test 10",
          score: "5",
        },
        {
          name: "test 11",
          score: "5",
        },
        {
          name: "test 12",
          score: "5",
        },
        {
          name: "test 13",
          score: "5",
        },
        {
          name: "test 14",
          score: "5",
        },
        {
          name: "test 15",
          score: "5",
        },
        {
          name: "test 16",
          score: "5",
        },
      ]
    }
  },
}
</script>



<style scoped>
</style>
