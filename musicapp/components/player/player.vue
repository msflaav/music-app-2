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
                <button class="btn btn-primary" @click="prev">Prev</button>

                <button
                  class="btn btn-primary"
                  v-if="!isplaying"
                  @click="play(music)"
                >
                  Play
                </button>
                <button class="btn btn-primary" @click="pause">Pause</button>
                <button class="btn btn-primary" @click="next">Next</button>
                <button class="btn btn-primary" @click="shuffle">
                  Shuffle
                </button>
              </div>
            </div>
            <div>
              Time until idle
              <v-idle @idle="onidle" :duration="30" />
            </div>
          </div>
        </div>
        <div class="col-md-6">
          <div class="card shadow">
            <h2 class="text-center">My playlist</h2>
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
                <tr
                  v-for="(music, index) in filteredMusic"
                  :key="`title-${index}`"
                >
                  <td v-html="highlightMatches(music.artist)"></td>
                  <td
                    v-html="
                      highlightMatches([...music.title].sort().join(', '))
                    "
                  ></td>
                </tr>
              </tbody>
            </table>
            <input
              type="text"
              placeholder="Search by title or artist"
              v-model="filter"
            />
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import Vue from "vue";
import Vidle from "v-idle";

Vue.use(Vidle);

export default {
  data() {
    return {
      current: {
        title: "",
      },
      song: true,
      isplaying: false,
      allMusic: null,
      index: 0,
      player: "",
      filter: "",
      music: [],
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
      this.index = Math.floor(Math.random() * this.allMusic.length);
      this.current = this.allMusic[this.index];
      this.play(this.current);
    },
    onidle() {
      alert("Idle mode on to save battery");
    },

    highlightMatches(text) {
      const matchExists = text
        .toLowerCase()
        .includes(this.filter.toLowerCase());
      if (!matchExists) return text;

      const re = new RegExp(this.filter, "ig");
      return text.replace(
        re,
        (matchedText) => `<strong>${matchedText}</strong>`
      );
    },
  },
  created() {
    if (process.client) {
      this.player = new Audio();
    }
    this.getAllSongs();
  },

  computed: {
    filteredMusic() {
      return this.music.filter((music) => {
        const title = music.title.toString().toLowerCase();
        const artist = music.artist.toLowerCase();
        const searchTerm = this.filter.toLowerCase();
        return artist.includes(searchTerm) || title.includes(searchTerm);
      });
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