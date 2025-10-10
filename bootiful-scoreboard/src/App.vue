<template>
  <div class="container-fluid">
    <div class="w-100 row m-0">
      <h1 class="col-md-12 text-center">ランキング</h1>
      <!--最新の記録-->
      <div class="d-flex flex-column align-items-center col-6 p-5">
        <div class="w-100 px-3">
          <div v-for="index in 3" class="w-100">
            <div v-if="(latestScore[index-1] ?? false)" class="w-100 my-4 border border-1 rounded shadow">
              <h3>{{ latestScore[index - 1].rank ?? "error" }} 位</h3>
              <div class="d-flex flex-row justify-content-center"><h5 class="flex-grow-1 flex-basis-0 text-end">{{ latestScore[index - 1].roomId ?? "UNKNOWN" }}</h5><h5 class="px-2">|</h5><h5 class="flex-grow-1 flex-basis-0 text-start">score: {{ latestScore[index - 1].score ?? 0 }}</h5></div>
            </div>
          </div>
        </div>
      </div>
      <!--1~9位まで-->
      <div class="d-flex flex-column align-items-center col-6 p-5">
        <div class="w-100 px-3">
          <div v-for="index in 10" class="w-100">
            <div v-if="(ranking[index-1] ?? false)" class="w-100 my-4 border border-1 rounded shadow">
              <h3>{{ index ?? "error" }} 位</h3>
              <div class="d-flex flex-row justify-content-center"><h5 class="flex-grow-1 flex-basis-0 text-end">{{ ranking[index - 1].roomId ?? "UNKNOWN" }}</h5><h5 class="px-2">|</h5><h5 class="flex-grow-1 flex-basis-0 text-start">score: {{ ranking[index - 1].score ?? 0 }}</h5></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
import { collection, getFirestore, onSnapshot } from "firebase/firestore";
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
const db = getFirestore(app);

const db_scores = collection(db, "scores");

const GET_RANKINGS_URL = 'https://us-central1-c4sbootiful.cloudfunctions.net/getGroupRanking';//'https://getgroupranking-vnjimsks5q-uc.a.run.app';

export default {
  data() {
    return {
      ranking: [],
      latestScore: [],
      unsubscribeDB: {},
    }
  },
  created() {
    this.unsubscribeDB = onSnapshot(db_scores, (snapshot) => {
      const to_sort = [];
      snapshot.docs.forEach((record) => {
        to_sort.push(record.data());
      });

      this.setRanking(to_sort);
    });
  },
  destroyed () {
    this.unsubscribeDB();
  },
  methods: {
    async setRanking(to_sort){
      this.ranking = to_sort.sort((first, second) =>{
        if (first.score == second.score) {
          return second.created_at - first.created_at;
        } else {
          return second.score - first.score;
        }
      }).slice();

      to_sort.forEach((record, index) => {
        record.rank = index + 1;
      });

      this.latestScore = to_sort.sort((first, second) => {
        if (first.created_at == second.created_at) {
          return second.score - first.socre;
        } else {
          return second.created_at - first.created_at;
        }
      });
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
        this.ranking = data.ranking;
        this.latestScore.unshift(data.latest);
        this.latestScore = this.latestScore.slice(0, 3);
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
