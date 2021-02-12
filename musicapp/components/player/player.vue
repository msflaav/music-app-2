<template>
  <section v-if="allMusic">
    <div class="container">
      <div class="row mb-5">
        <div class="col-md-12">
          <h3 class="text-center">Player</h3>
        </div>
      </div>
      <div class="row mt-5">
        <div class="col-md-6">
          <img
            src="https://images.macrumors.com/t/sqodWOqvWOvq6cU8t2ahMlU4AJM=/1600x0/article-new/2018/05/apple-music-note.jpg"
            class="image"
          />
          <div v-if="this.current" class="card player_card">
            <div class="card-body">
              <h6 class="card-title">
                <b>{{ this.current.title }} - {{ this.current.artist }}</b>
              </h6>
              <div>
                <!-- <i class="fas fa-backward control mr-4" @click="prev"></i> -->
                <button class="btn btn-primary" @click="prev">Prev</button>
                <!-- <i class="fas fa-play play" v-if="!isplaying" @click="play"></i> -->
                <button
                  class="btn btn-primary"
                  v-if="!isplaying"
                  @click="play(music)"
                >
                  Play
                </button>
                <!-- <i class="fas_fa-pause_play" @click="pause" v-else></i> -->
                <button class="btn btn-primary" @click="pause">Pause</button>
                <!-- <i class="fas fa-forward control ml-4" @click="next"></i> -->
                <button class="btn btn-primary" @click="next">Next</button>
                <button class="btn btn-primary" @click="shuffle">
                  Shuffle
                </button>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md-6">
          <div class="card shadow">
            <table class="table">
              <thead>
                <tr>
                  <th scope="col">#</th>
                  <th scope="col">Title</th>
                  <th scope="col">Artist</th>
                  <th scope="col">Action</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(music, index) in allMusic" :key="index">
                  <th scope="row">{{ index + 1 }}</th>
                  <td>{{ music.title }}</td>
                  <td>{{ music.artist }}</td>
                  <td>
                    <button class="btn btn-primary" @click="play(music)">
                      Play
                    </button>
                    <button class="btn btn-primary" @click="pause(music)">
                      Pause
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
            <!-- <input
              type="text"
              id="myInput"
              onkeyup="searchSong()"
              placeholder="Search for songs.."
              title="Type in a name"
            /> -->

            <div class="w-full px-3 mb-6">
              <!-- <label
                class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2"
                for="name"
              >
              </label> -->
              <div class="relative mb-3">
                <input
                  v-model="keyword"
                  class="block appearance-none w-full bg-gray-200 border border-gray-200 text-gray-700 py-3 px-4 pr-8 rounded leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
                  id="name"
                  placeholder="Search for songs.."
                />
                <div
                  class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700"
                >
                  <svg
                    class="h-4 w-4"
                    xmlns="http://www.w3.org/2000/svg"
                    width="24"
                    height="24"
                    viewBox="0 0 24 24"
                    fill="none"
                    stroke="#000000"
                    stroke-width="2"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                  >
                    <circle cx="11" cy="11" r="8"></circle>
                    <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
                  </svg>
                </div>
              </div>
              <button
                @click.prevent="checkName"
                class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
              >
                Search
              </button>
            </div>

            <!-- <ul class="px-3 list-disc">
                  <li v-for="people in peoples" :key="people.url">
                    {{ people.name }} - Height: {{ people.height }} Mass:
                    {{ people.mass }}
                  </li>
                </ul> -->
            <ul class="px-3 list-disc">
              <li v-for="music in music" :key="music.url">
                {{ music.title }} - Artist: {{ music.artist }}
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import axios from "axios";
import { debounce } from "lodash";

export default {
  data() {
    return {
      current: {
        title: "",
        artist: "",
        keyword: "",
        music: [],
      },
      song: true,
      isplaying: false,
      allMusic: null,
      index: 0,
      player: "",
    };
  },

  methods: {
    async initPlayer() {
      if (this.allMusic !== []) {
        this.current = await this.allMusic[this.index];
        this.player.src = `http://localhost:4000/${this.current.music.path}`;
      } else {
        this.song = true;
      }
    },
    async getAllSongs() {
      try {
        let response = await this.$axios.$get("/music");
        console.log(response);
        if (response === []) {
          this.song = true;
          this.current = null;
        } else {
          this.song = false;
          this.allMusic = response;
        }
        await this.initPlayer();
      } catch (err) {
        this.current = null;
        console.log(err);
      }
    },
    play(song) {
      console.log(song);
      if (song) {
        this.current = song;
        this.player.src = `http://localhost:4000/${this.current.music.path}`;
      }
      this.player.play();
      this.isplaying = true;
    },
    pause() {
      if (this.player.paused) {
        this.player.play();
        this.isplaying = true;
      } else {
        this.player.pause();
        this.isplaying = false;
      }
    },
    next() {
      this.index++;
      if (this.index > this.allMusic.length - 1) {
        this.index = 0;
      }
      this.current = this.allMusic[this.index];
      this.play(this.current);
    },
    prev() {
      this.index--;
      if (this.index < 0) {
        this.index = this.allMusic.length - 1;
      }
      this.current = this.allMusic[this.index];
      this.play(this.current);
    },
    shuffle() {
      this.index = Math.floor(Math.random() * this.allMus.length);
      this.current = this.allMusic[this.index];
      this.play(this.current);
    },
    checkName() {
      console.log(`Checking name: ${this.keyword}`);
      axios
        .get("http://localhost:4000/music/", {
          params: {
            search: this.keyword,
          },
        })
        .then((res) => {
          // eslint-disable-next-line no-console
          console.log(res.data.results);
          this.music = res.data.results;
        })
        .catch((err) => {
          // eslint-disable-next-line no-console
          console.log(err);
        });
    },
  },
  created() {
    if (process.client) {
      this.player = new Audio();
    }
    this.getAllSongs();
    this.debounceName = debounce(this.checkName, 1000);
  },
  watch: {
    keyword() {
      if (!this.keyword) return;
      this.debounceName();
    },
  },
};
</script>

<style  scoped>
.image {
  border-radius: 5px !important;
  position: relative;
  height: 300px;
  width: 100%;
}
.player_card {
  text-align: center;
  bottom: 20px;
  margin: 0px 40px;
}
.text-muted {
  font-size: 15px;
}
.play {
  font-size: 40px;
}
.control {
  font-size: 25px;
}
</style>