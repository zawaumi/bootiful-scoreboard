<template>
  <div class="container">
    <div class="w-100 row m-0">
      <h1 class="col-md-12 text-center">ランキング</h1>
      <!--1~9位まで-->
      <div class="d-flex flex-column align-items-center col-6 p-5">
        <div v-for="index in 9">
          <div v-if="(teams[index-1] ?? false)" class="w-100 m-2 border border-1 rounded shadow">
            <h3>{{ index }} 位</h3>
            <div class="d-flex flex-row justify-content-center"><h5 class="flex-grow-1 flex-basis-0 text-end">{{ teams[index - 1].data().roomId ?? UNKNOWN }}</h5><h5 class="px-2">|</h5><h5 class="flex-grow-1 flex-basis-0 text-start">score: {{ teams[index - 1].data().score ?? 0 }}</h5></div>
          </div>
        </div>
      </div>
      <!--10位以降-->
      <div class="d-flex flex-column align-items-center col-6 p-5">
        <div v-for="(team, index) in teams">
          <div v-if="9 <= index">
            {{ index + 1 }} 位{{ team.data().roomId }}score: {{ team.data().score }}</div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

const firebaseConfig = {
  apiKey: "AIzaSyAUPmxyioDa0pwQO_enfiv62Gb_v4HG9i0",
  authDomain: "c4sbootiful.firebaseapp.com",
  databaseURL: "https://c4sbootiful-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "c4sbootiful",
  storageBucket: "c4sbootiful.firebasestorage.app",
  messagingSenderId: "1006642710358",
  appId: "1:1006642710358:web:951265dd7e9e87e0c578f7",
  measurementId: "G-ZNQGLVHD37"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const analytics = getAnalytics(app);

const GET_RANKINGS_URL = 'https://us-central1-c4sbootiful.cloudfunctions.net/getGroupRanking';//'https://getgroupranking-vnjimsks5q-uc.a.run.app';

export default {
  data() {
    return {
      teams: []
    }
  },
  created() {
      this.callGetGroupRanking();
  },
  methods: {
    sort_teams(score){
      return score;
    },
    async callGetGroupRanking(){
      const payload = {
        number: 10
      };

      try {
        const response = await fetch(GET_RANKINGS_URL, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(payload),
        });

        if (!response.ok) {
          throw new Error('HTTPerror: ${response.status}');
        }

        const data = await response.json();
        console.log(data);
      } catch (error) {
        this.teams = [];
        console.error('error with getting ranking from firebase: ',error);
      }
    }
  },
}
</script>



<style scoped>
.flex-basis-0{
  flex-basis: 0;
}
</style>
