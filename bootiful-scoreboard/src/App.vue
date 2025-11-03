<template>
  <img src="./assets/logo.png" alt="bootiful-scoreboard" class="h-auto w-50 z-2">
  <div class="w-100" style="min-height: 100vh; height: max-content;">
    <div class="position-absolute start-0 end-0 z-0 overflow-hidden" style="top: 25%; bottom: 50%;" id="ghostLane1"></div>
    <div class="position-absolute start-0 end-0 z-0 overflow-hidden" style="top: 50%; bottom: 25%;" id="ghostLane2"></div>
    <div class="position-absolute start-0 end-0 z-0 overflow-hidden" style="top: 75%; bottom: 0%;" id="ghostLane3"></div>
    <div class="position-absolute container-fluid z-1 start-0 end-0">
      <div class="w-100 row m-0">
        <!--最新の記録-->
        <div class="d-flex flex-column align-items-center col-4">
          <h1 class="w-100 text-center my-2 text-white">最新の記録</h1>
          <div class="w-100 px-5 pb-5 pt-1">
            <div class="w-100 px-3">
              <div v-for="index in 3" class="w-100">
                <div v-if="(latestScore[index-1] ?? false)" class="w-100 my-4 border border-1 rounded shadow bg-white">
                  <h3>{{ latestScore[index - 1].rank ?? "error" }} 位</h3>
                  <div class="d-flex flex-row justify-content-center"><h5 class="flex-grow-1 flex-basis-0 text-end">{{ latestScore[index - 1].roomId ?? "UNKNOWN" }}</h5><h5 class="px-2">|</h5><h5 class="flex-grow-1 flex-basis-0 text-start">score: {{ latestScore[index - 1].score ?? 0 }}</h5></div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-8">
          <h1 class="w-100 text-center my-2 text-white">ランキング</h1>
          <div class="w-100 row m-0">
            <!--1~9位まで-->
            <div class="d-flex flex-column align-items-center col-6 px-5 pb-5 pt-1">
              <div class="w-100 px-3">
                <div v-for="index in 5" class="w-100">
                  <div v-if="(ranking[index-1] ?? false)" class="w-100 my-4 border border-1 rounded shadow bg-white">
                    <h3>{{ ranking[index-1].rank ?? "error" }} 位</h3>
                    <div class="d-flex flex-row justify-content-center"><h5 class="flex-grow-1 flex-basis-0 text-end">{{ ranking[index - 1].roomId ?? "UNKNOWN" }}</h5><h5 class="px-2">|</h5><h5 class="flex-grow-1 flex-basis-0 text-start">score: {{ ranking[index - 1].score ?? 0 }}</h5></div>
                  </div>
                </div>
              </div>
            </div>
            <!--5~10位まで-->
            <div class="d-flex flex-column align-items-center col-6 px-5 pb-5 pt-1">
              <div class="w-100 px-3">
                <div v-for="index in 5" class="w-100">
                  <div v-if="(ranking[index + 4] ?? false)" class="w-100 my-4 border border-1 rounded shadow bg-white">
                    <h3>{{ ranking[index + 4].rank ?? "error" }} 位</h3>
                    <div class="d-flex flex-row justify-content-center"><h5 class="flex-grow-1 flex-basis-0 text-end">{{ ranking[index + 4].roomId ?? "UNKNOWN" }}</h5><h5 class="px-2">|</h5><h5 class="flex-grow-1 flex-basis-0 text-start">score: {{ ranking[index + 4].score ?? 0 }}</h5></div>
                  </div>
                </div>
              </div>
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
      unsubscribeDB: {}
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

    document.addEventListener('keyup', (event) => {
      const target_div = document.getElementById("app");
      if(event.key == 'f' && !document.fullscreenElement) {
        if (target_div.requestFullscreen) {
          target_div.requestFullscreen();
        } else if (target_div.mozRequestFullScreen) { // Firefox
          target_div.mozRequestFullScreen();
        } else if (target_div.webkitRequestFullscreen) { // Chrome, Safari, Opera
          target_div.webkitRequestFullscreen();
        } else if (target_div.msRequestFullscreen) { // IE/Edge
          target_div.msRequestFullscreen();
        }
      }
    })
    //お化け召喚
    const ghostLane1 = setInterval(() => {this.summon_ghost("ghostLane1")}, 15000);
    setTimeout(() => {
      const ghostLane2 = setInterval(() => {this.summon_ghost("ghostLane2")}, 15000);
    }, 5000);
    setTimeout(() => {
      const ghostLane3 = setInterval(() => {this.summon_ghost("ghostLane3")}, 15000);
    }, 10000);
  },
  destroyed () {
    this.unsubscribeDB();
    clearInterval(ghostLane1);
    clearInterval(ghostLane2);
    clearInterval(ghostLane3);
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
    },
    summon_ghost(area_id) {
      let lane = document.getElementById(area_id ?? "");
        lane.classList.remove("move-up-down");
      if (lane.firstChild) {lane.removeChild(lane.firstChild);}
      let ghost = document.createElement("img");
      ghost.classList.add("ghost");
      if (0.5 < Math.random()){
        let random = Math.random();
        if (random < 0.25) {
          ghost.src = "./src/assets/gray_left.png";
        } else if (random < 0.5) {
          ghost.src = "./src/assets/green_left.png";
        } else if (random < 0.75) {
          ghost.src = "./src/assets/yellow_left.png";
        } else {
          ghost.src = "./src/assets/red_left.png";
        }
        ghost.classList.add("ghost-at-right");
        lane.appendChild(ghost);
        console.log("call append.");
        setTimeout(() =>{
          ghost.classList.add("move-left");
          lane.classList.add("move-up-down");
        }, Math.random() * 7000);
      } else {
        let random = Math.random();
        if (random < 0.25) {
          ghost.src = "./src/assets/gray_right.png";
        } else if (random < 0.5) {
          ghost.src = "./src/assets/green_right.png";
        } else if (random < 0.75) {
          ghost.src = "./src/assets/yellow_right.png";
        } else {
          ghost.src = "./src/assets/red_right.png";
        }
        ghost.classList.add("ghost-at-left");
        lane.appendChild(ghost);
        setTimeout(() =>{
          ghost.classList.add("move-right");
          lane.classList.add("move-up-down");
        }, Math.random() * 7000);
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
<style>
.ghost{
  position: absolute;
  height: 100%;
  transition: all 7s 0s ease;
}
.ghost-at-left{
  right: 100%;
}
.ghost-at-right{
  left: 100%;
}
@keyframes slide-up-down {
  0%{
    transform: translateY(0);
  }
  50%{
    transform: translateY(5vh);
  }
  100%{
    transform: translateY(0);
  }
}
.move-up-down{
  animation: slide-up-down 2s ease infinite;
}
@keyframes slide-left{
  0%{
    transform: translateX(0);
  }
  100%{
    transform: translateX(-200vw);
  }
}
.move-left {
  animation: slide-left 10s linear forwards;
}
@keyframes slide-right{
  0%{
    transform: translateX(0);
  }
  100%{
    transform: translateX(200vw);
  }
}
.move-right {
  animation: slide-right 10s linear forwards;
}
</style>
