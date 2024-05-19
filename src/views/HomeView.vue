<template>
  <div class="home">
    <div class="px-[120px] max-w-[1440px] mx-auto mt-8 mb-40 text-white">
      <div class="mx-auto flex justify-between">
        <div class="w-[714px]">
          <div class="">
            <span class="font-semibold text-[24px]">All songs</span>
            <div class="mt-6 w-full h-[280px] relative">
              <img src="../assets/images/bg-1.svg" alt="" />
              <div
                @click="playAll()"
                class="w-[235px] h-[60px] absolute bottom-6 left-6 text-black bg-white rounded-full flex items-center justify-center cursor-pointer hover:bg-[#F2F2F2] transition"
              >
                <img
                  src="../assets/images/play-all.svg"
                  alt=""
                  class="mr-3 w-4"
                />
                <span>Listen now</span>
              </div>
            </div>
          </div>
          <div class="mt-[60px]">
            <span class="font-semibold text-2xl">Playlists for you</span>
            <div class="mt-6 flex justify-between items-center">
              <div
                @click="selectVpop()"
                class="w-[222px] relative playlist rounded-[36px] overflow-hidden cursor-pointer"
              >
                <img src="../assets/images/pop.svg" alt="" class="transition" />
                <span class="absolute block top-6 left-7 font-semibold text-xl"
                  >V-Pop</span
                >
                <span class="absolute block bottom-6 left-7"
                  >{{ vpop.length }} tracks</span
                >
              </div>
              <div
                @click="selectBolero()"
                class="w-[222px] relative playlist rounded-[36px] overflow-hidden cursor-pointer"
              >
                <img
                  src="../assets/images/bolero.svg"
                  alt=""
                  class="transition"
                />
                <span class="absolute block top-6 left-7 font-semibold text-xl"
                  >Bolero</span
                >
                <span class="absolute block bottom-6 left-7"
                  >{{ bolero.length }} tracks</span
                >
              </div>
              <div
                @click="selectHiphop()"
                class="w-[222px] relative playlist rounded-[36px] overflow-hidden cursor-pointer"
              >
                <img
                  src="../assets/images/hiphop.svg"
                  alt=""
                  class="transition"
                />
                <span class="absolute block top-6 left-7 font-semibold text-xl"
                  >Hip hop</span
                >
                <span class="absolute block bottom-6 left-7"
                  >{{ hiphop.length }} tracks</span
                >
              </div>
            </div>
          </div>
        </div>
        <div class="w-[427px]">
          <div class="flex justify-between items-center">
            <div class="text-2xl font-semibold">
              Now playing
              <img
                @click="copyCurrentUrl()"
                src="../assets/images/share-white.svg"
                alt=""
                class="inline-block ml-3"
              />
            </div>
            <span class="block">{{ currentPlaylistText }}</span>
          </div>
          <div class="mt-3 overflow-y-scroll max-h-[634px]">
            <div
              @click="playAudio(index)"
              v-for="(item, index) in currentPlaylist"
              :key="index"
              :class="{ active: isPlaying === index }"
              class="mt-4 h-[88px] w-[96%] bg-[#232220] rounded-[20px] flex items-center px-3 py-3 justify-between hover:bg-[#504F4D] cursor-pointer transition"
            >
              <div class="flex items-center">
                <div
                  class="w-16 h-16 rounded-lg my-auto overflow-hidden relative"
                >
                  <img :src="item.image" alt="" />
                  <img
                    v-if="isPlaying === index && isPausing"
                    src="../assets/images/play-white.svg"
                    alt=""
                    class="top-1/2 left-1/2 absolute -translate-x-1/2 -translate-y-1/2 w-[18px] transition"
                  /><img
                    v-if="isPlaying === index && !isPausing"
                    src="../assets/images/pause-white.svg"
                    alt=""
                    class="top-1/2 left-1/2 absolute -translate-x-1/2 -translate-y-1/2 transition"
                  />
                </div>
                <div class="ml-5">
                  <span class="block font-medium truncate w-[200px]">{{
                    item.song
                  }}</span>
                  <span class="block font-light text-sm">{{
                    item.singer
                  }}</span>
                </div>
              </div>
              <span class="block mr-7">{{ item.duration }}</span>
              <audio
                @ended="playNext(index)"
                @timeupdate="updateCurrentTime(index)"
                ref="audios"
                class="music"
              >
                <source :src="item.src" type="audio/mpeg" />
              </audio>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div
      v-show="isPlaying !== ''"
      class="h-[100px] bg-white text-black fixed bottom-0 left-0 right-0"
    >
      <div
        class="max-w-[1440px] mx-auto h-full py-5 px-[120px] flex items-center justify-between"
      >
        <div class="flex items-center w-[240px]">
          <div
            class="overflow-hidden w-[68px] h-[68px] rounded-full bg-cover bg-no-repeat bg-center"
          >
            <img
              :src="currentSong.image"
              alt=""
              class="w-[68px] h-[68px] bg-cover bg-no-repeat bg-center"
            />
          </div>
          <div class="ml-6">
            <span class="font-medium block truncate max-w-[150px]">{{
              currentSong.song
            }}</span>
            <span class="font-light text-sm block truncate max-w-[170px]">{{
              currentSong.singer
            }}</span>
          </div>
        </div>
        <div class="w-[140px] flex gap-7 items-center">
          <img
            @click="playPre(currentIndex)"
            src="../assets/images/pre.svg"
            alt=""
            class="cursor-pointer"
          />
          <img
            v-show="isPausing"
            @click="playAudio(currentIndex)"
            src="../assets/images/play-black.svg"
            alt=""
            class="cursor-pointer"
          />
          <img
            v-show="!isPausing"
            @click="playAudio(currentIndex)"
            src="../assets/images/pause-black.svg"
            alt=""
            class="cursor-pointer"
          />
          <img
            @click="playNext(currentIndex)"
            src="../assets/images/next.svg"
            alt=""
            class="cursor-pointer"
          />
        </div>
        <div class="w-[412px] flex items-center">
          <input
            @input="changeTime(currentIndex)"
            v-model="currentTime"
            type="range"
            min="0"
            :max="duration"
            class="cursor-pointer w-[352px] slide-song"
          />
          <span class="block ml-4">{{
            formatDuration(duration - currentTime)
          }}</span>
        </div>
        <div class="flex items-center">
          <img src="../assets/images/volume.svg" alt="" />
          <input
            @input="changeVolume(currentIndex)"
            v-model="audioVolume"
            type="range"
            min="0"
            max="1"
            step="0.01"
            class="cursor-pointer w-[100px] ml-3 slide-volume"
          />
        </div>
        <div class="flex">
          <img
            @click="downloadSong(currentIndex)"
            src="../assets/images/download.svg"
            alt=""
            class="cursor-pointer"
          />
          <img
            @click="copyCurrentUrl()"
            src="../assets/images/share-black.svg"
            alt=""
            class="ml-3 cursor-pointer"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import { ref, reactive, watch } from "vue";
export default {
  setup() {
    const audios = ref("");
    const vpop = reactive([
      {
        id: 1,
        song: "Chung ta cua tuong lai",
        singer: "Son Tung M-TP",
        image: require("@/assets/playlists/v-pop/chung-ta-cua-tuong-lai/image.jpg"),
        duration: "4:09",
        src: require("@/assets/playlists/v-pop/chung-ta-cua-tuong-lai/song.mp3"),
      },
      {
        id: 2,
        song: "Hong nhan",
        singer: "J97",
        image: require("@/assets/playlists/v-pop/hong-nhan/image.jpg"),
        duration: "3:15",
        src: require("@/assets/playlists/v-pop/hong-nhan/song.mp3"),
      },
      {
        id: 3,
        song: "Le cay",
        singer: "Du Thien",
        image: require("@/assets/playlists/v-pop/le-cay/image.jpg"),
        duration: "4:03",
        src: require("@/assets/playlists/v-pop/le-cay/song.mp3"),
      },
      {
        id: 4,
        song: "Nang am xa dan",
        singer: "Son Tung M-TP",
        image: require("@/assets/playlists/v-pop/nang-am-xa-dan/image.jpg"),
        duration: "3:08",
        src: require("@/assets/playlists/v-pop/nang-am-xa-dan/song.mp3"),
      },
      {
        id: 5,
        song: "Ngay em cuoi",
        singer: "Chau Khai Phong",
        image: require("@/assets/playlists/v-pop/ngay-em-cuoi/image.jpg"),
        duration: "4:31",
        src: require("@/assets/playlists/v-pop/ngay-em-cuoi/song.mp3"),
      },
      {
        id: 6,
        song: "Noi dau ngu tri",
        singer: "Le Quyen",
        image: require("@/assets/playlists/v-pop/noi-dau-ngu-tri/image.jpg"),
        duration: "4:41",
        src: require("@/assets/playlists/v-pop/noi-dau-ngu-tri/song.mp3"),
      },
      {
        id: 7,
        song: "Thien ly oi",
        singer: "J97",
        image: require("@/assets/playlists/v-pop/thien-ly-oi/image.jpg"),
        duration: "3:40",
        src: require("@/assets/playlists/v-pop/thien-ly-oi/song.mp3"),
      },
    ]);
    const bolero = reactive([
      {
        id: 1,
        song: "Dap mo cuoc tinh",
        singer: "Chu Hoang Tuan",
        image: require("@/assets/playlists/bolero/dap-mo-cuoc-tinh/image.jpg"),
        duration: "5:03",
        src: require("@/assets/playlists/bolero/dap-mo-cuoc-tinh/song.mp3"),
      },
      {
        id: 2,
        song: "Hoa su nha nang",
        singer: "Quang Le",
        image: require("@/assets/playlists/bolero/hoa-su-nha-nang/image.jpg"),
        duration: "4:34",
        src: require("@/assets/playlists/bolero/hoa-su-nha-nang/song.mp3"),
      },
      {
        id: 3,
        song: "Sau tim thiep hong",
        singer: "Quang Le",
        image: require("@/assets/playlists/bolero/sau-tim-thiep-hong/image.jpg"),
        duration: "4:46",
        src: require("@/assets/playlists/bolero/sau-tim-thiep-hong/song.mp3"),
      },
      {
        id: 4,
        song: "Ve dau mai toc nguoi thuong",
        singer: "Cam Ly",
        image: require("@/assets/playlists/bolero/ve-dau-mai-toc-nguoi-thuong/image.jpg"),
        duration: "5:37",
        src: require("@/assets/playlists/bolero/ve-dau-mai-toc-nguoi-thuong/song.mp3"),
      },
      {
        id: 5,
        song: "Tuong tu nang ca si",
        singer: "Quang Le",
        image: require("@/assets/playlists/bolero/tuong-tu-nang-ca-si/image.jpg"),
        duration: "5:10",
        src: require("@/assets/playlists/bolero/tuong-tu-nang-ca-si/song.mp3"),
      },
      {
        id: 6,
        song: "Vung la me bay",
        singer: "Duong Hong Loan",
        image: require("@/assets/playlists/bolero/vung-la-me-bay/image.jpg"),
        duration: "4:22",
        src: require("@/assets/playlists/bolero/vung-la-me-bay/song.mp3"),
      },
    ]);
    const hiphop = reactive([
      {
        id: 1,
        song: "Ngang nhien",
        singer: "Chiennhatlang",
        image: require("@/assets/playlists/hip-hop/ngang-nhien/image.jpg"),
        duration: "3:53",
        src: require("@/assets/playlists/hip-hop/ngang-nhien/song.mp3"),
      },
      {
        id: 2,
        song: "Phu Du",
        singer: "Chiennhatlang",
        image: require("@/assets/playlists/hip-hop/phu-du/image.jpg"),
        duration: "3:40",
        src: require("@/assets/playlists/hip-hop/phu-du/song.mp3"),
      },
      {
        id: 3,
        song: "Man's not hot",
        singer: "Big Shaq",
        image: require("@/assets/playlists/hip-hop/mans-not-hot/image.jpg"),
        duration: "2:19",
        src: require("@/assets/playlists/hip-hop/mans-not-hot/song.mp3"),
      },
    ]);
    const combinedPlaylist = [...vpop, ...bolero, ...hiphop];
    const currentPlaylist = ref(combinedPlaylist);
    const currentPlaylistText = ref("All songs");
    const currentSong = ref("");
    const currentIndex = ref("");
    const currentTime = ref(0);
    const duration = ref(null);
    const audioVolume = ref(1);
    const isPlaying = ref("");
    const isPausing = ref(false);

    function refreshAudio() {
      isPlaying.value = "";
      audios.value.forEach((audio) => {
        audio.pause();
        audio.currentTime = 0;
        audio.load();
      });
    }

    function playAll() {
      currentPlaylistText.value = "All songs";
      currentPlaylist.value = combinedPlaylist;
      refreshAudio();
      playAudio(0);
    }

    function selectVpop() {
      currentPlaylistText.value = "V-Pop";
      currentPlaylist.value = vpop;
      refreshAudio();
      playAudio(0);
    }

    function selectBolero() {
      currentPlaylistText.value = "Bolero";
      currentPlaylist.value = bolero;
      refreshAudio();
      playAudio(0);
    }

    function selectHiphop() {
      currentPlaylistText.value = "Hip hop";
      currentPlaylist.value = hiphop;
      refreshAudio();
      playAudio(0);
    }

    function playAudio(index) {
      audios.value.forEach((item, idx) => {
        if (index === idx) {
          if (!isNaN(item.duration)) {
            duration.value = item.duration;
          }
        }
      });
      isPlaying.value = index;
      audios.value.forEach((item, idx) => {
        if (index !== idx) {
          item.pause();
          item.currentTime = 0;
        } else {
          item.volume = audioVolume.value;
          if (!isNaN(item.duration)) {
            duration.value = item.duration;
          }
          currentIndex.value = index;
          item.addEventListener("loadedmetadata", () => {
            duration.value = item.duration;
          });
          currentPlaylist.value.forEach((item, id) => {
            if (index === id) {
              currentSong.value = item;
            }
          });

          if (item.paused) {
            item.play();
            isPausing.value = false;
          } else {
            item.pause();
            isPausing.value = true;
          }
        }
      });
    }

    function playPre(index) {
      let tmp = index - 1;
      if (tmp < 0) {
        tmp = audios.value.length - 1;
      }
      playAudio(tmp);
    }

    function playNext(index) {
      let tmp = index + 1;
      if (tmp >= audios.value.length) {
        tmp = 0;
      }
      playAudio(tmp);
    }

    function changeTime(index) {
      audios.value.forEach((item, idx) => {
        if (index === idx) {
          item.currentTime = currentTime.value;
        }
      });
    }

    function updateCurrentTime(index) {
      audios.value.forEach((item, idx) => {
        if (index === idx && !isPausing.value) {
          // Chỉ cập nhật currentTime nếu không đang tạm dừng bài hát
          currentTime.value = item.currentTime;
        }
      });
    }

    function changeVolume(index) {
      audios.value.forEach((item, idx) => {
        if (index === idx) {
          item.volume = audioVolume.value;
        }
      });
    }

    function downloadSong(index) {
      const selectedSong = currentPlaylist.value[index];
      const songUrl = selectedSong.src;
      const link = document.createElement("a");
      link.href = songUrl;
      link.download = selectedSong.song + ".mp3";
      link.click();
    }

    function copyCurrentUrl() {
      const currentUrl = window.location.href;
      navigator.clipboard
        .writeText(currentUrl)
        .then(() => {
          alert("URL đã được sao chép!");
        })
        .catch((error) => {
          console.error("Lỗi khi sao chép URL:", error);
        });
    }

    function formatDuration(seconds) {
      const min = Math.floor(seconds / 60);
      const sec = Math.floor(seconds % 60);
      return `-${min}:${sec < 10 ? "0" : ""}${sec}`;
    }

    document.addEventListener("DOMContentLoaded", () => {
      const sliderEl = document.querySelector(".slide-song");
      const sliderVol = document.querySelector(".slide-volume");

      const updateSliderBackground = (slider) => {
        const progress = (slider.value / slider.max) * 100;
        slider.style.background = `linear-gradient(to right, #000 ${progress}%, #ccc ${progress}%)`;
      };
      updateSliderBackground(sliderEl);
      updateSliderBackground(sliderVol);

      watch(currentTime, () => {
        updateSliderBackground(sliderEl);
      });

      sliderVol.addEventListener("input", (event) => {
        updateSliderBackground(event.target);
      });
    });

    return {
      vpop,
      bolero,
      hiphop,
      isPlaying,
      isPausing,
      currentPlaylist,
      currentSong,
      currentIndex,
      currentPlaylistText,
      currentTime,
      duration,
      audioVolume,
      audios,
      playAll,
      playAudio,
      playNext,
      playPre,
      changeTime,
      updateCurrentTime,
      changeVolume,
      refreshAudio,
      selectVpop,
      selectBolero,
      selectHiphop,
      downloadSong,
      copyCurrentUrl,
      formatDuration,
    };
  },
};
</script>

<style scoped>
.active {
  background-color: #504f4d;
}

.playlist:hover img {
  transform: scale(1.1);
}

.slide-song {
  -webkit-appearance: none;
  appearance: none;
  width: 352px;
  cursor: pointer;
  outline: none;
  border-radius: 15px;
  height: 2px;
  background: #ccc;
}

.slide-song::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  height: 16px;
  width: 16px;
  background-color: #000;
  border-radius: 50%;
  transition: 0.2s ease-in-out;
}

.slide-song::-webkit-slider-thumb:hover {
  box-shadow: 0 0 0 8px rgba(0, 0, 0, 0.07);
}
.slide-song:active::-webkit-slider-thumb {
  box-shadow: 0 0 0 10px rgba(0, 0, 0, 0.1);
}
.slide-song:focus::-webkit-slider-thumb {
  box-shadow: 0 0 0 10px rgba(0, 0, 0, 0.1);
}

.slide-song::-moz-range-thumb:hover {
  box-shadow: 0 0 0 10px rgba(0, 0, 0, 0.07);
}
.slide-song:active::-moz-range-thumb {
  box-shadow: 0 0 0 10px rgba(0, 0, 0, 0.1);
}
.slide-song:focus::-moz-range-thumb {
  box-shadow: 0 0 0 10px rgba(0, 0, 0, 0.1);
}

.slide-volume {
  -webkit-appearance: none;
  appearance: none;
  cursor: pointer;
  outline: none;
  border-radius: 15px;
  height: 2px;
  background: #ccc;
}

.slide-volume::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  height: 16px;
  width: 16px;
  background-color: #000;
  border-radius: 50%;
  transition: 0.2s ease-in-out;
}

.slide-volume::-webkit-slider-thumb:hover {
  box-shadow: 0 0 0 8px rgba(0, 0, 0, 0.07);
}
.slide-volume:active::-webkit-slider-thumb {
  box-shadow: 0 0 0 10px rgba(0, 0, 0, 0.1);
}
.slide-volume:focus::-webkit-slider-thumb {
  box-shadow: 0 0 0 10px rgba(0, 0, 0, 0.1);
}

.slide-volume::-moz-range-thumb:hover {
  box-shadow: 0 0 0 10px rgba(0, 0, 0, 0.07);
}
.slide-volume:active::-moz-range-thumb {
  box-shadow: 0 0 0 10px rgba(0, 0, 0, 0.1);
}
.slide-volume:focus::-moz-range-thumb {
  box-shadow: 0 0 0 10px rgba(0, 0, 0, 0.1);
}

::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background-color: #1f1d1b;
  border-radius: 10px;
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: #2d2c2b;
  border-radius: 10px;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #2d2c2b;
}
</style>
