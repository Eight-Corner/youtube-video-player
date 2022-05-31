<template>
  <div>
    <h1 style="text-align: center;"> YOUTUBE - VIDEO - PLAYER </h1>
    <div class="video-container"  data-volume-level="high"
         :class="[{'paused' : paused}, {'theater' : theaterMode}, {'full-screen': fullscreen}, {'muted': volume === 0}, {'captions': captions}]"
         ref="video_container">
      <div class="video-controls-container">
        <img class="thumbnail-img" :src="thumbnailImg" ref="thumbnailImg" />
        <div class="timeline-container"  @mousemove="handleTimelineUpdate($event)" @mousedown="toggleScrubbing($event)" ref="timelineContainer">
          <div class="timeline">
            <img class="preview-img" :src="previewImg" ref="previewImg"/>
            <div class="thumb-indicator"></div>
          </div>
        </div>
        <div class="controls">
          <button class="play-pause-btn" @click="togglePlay()">
            <svg class="play-icon" viewBox="0 0 24 24">
              <path fill="currentColor" d="M8,5.14V19.14L19,12.14L8,5.14Z" />
            </svg>
            <svg class="pause-icon" viewBox="0 0 24 24">
              <path fill="currentColor" d="M14,19H18V5H14M6,19H10V5H6V19Z" />
            </svg>
          </button>

          <div class="volume-container">
            <button class="mute-btn" ref="muteBtn" @click="toggleMute">
              <svg class="volume-high-icon" viewBox="0 0 24 24">
                <path fill="currentColor" d="M14,3.23V5.29C16.89,6.15 19,8.83 19,12C19,15.17 16.89,17.84 14,18.7V20.77C18,19.86 21,16.28 21,12C21,7.72 18,4.14 14,3.23M16.5,12C16.5,10.23 15.5,8.71 14,7.97V16C15.5,15.29 16.5,13.76 16.5,12M3,9V15H7L12,20V4L7,9H3Z" />
              </svg>
              <svg class="volume-low-icon" viewBox="0 0 24 24">
                <path fill="currentColor" d="M5,9V15H9L14,20V4L9,9M18.5,12C18.5,10.23 17.5,8.71 16,7.97V16C17.5,15.29 18.5,13.76 18.5,12Z" />
              </svg>
              <svg class="volume-muted-icon" viewBox="0 0 24 24">
                <path fill="currentColor" d="M12,4L9.91,6.09L12,8.18M4.27,3L3,4.27L7.73,9H3V15H7L12,20V13.27L16.25,17.53C15.58,18.04 14.83,18.46 14,18.7V20.77C15.38,20.45 16.63,19.82 17.68,18.96L19.73,21L21,19.73L12,10.73M19,12C19,12.94 18.8,13.82 18.46,14.64L19.97,16.15C20.62,14.91 21,13.5 21,12C21,7.72 18,4.14 14,3.23V5.29C16.89,6.15 19,8.83 19,12M16.5,12C16.5,10.23 15.5,8.71 14,7.97V10.18L16.45,12.63C16.5,12.43 16.5,12.21 16.5,12Z" />
              </svg>
            </button>
            <input class="volume-slider" ref="volumeSlider" v-model="volume" @change="volumeChange($event)"
                   type="range" min="0" max="1" step="any" value="1" />
          </div>
          <div class="duration-container" ref="videoDuration">
            <div class="current-time">{{ currentTime }}</div>
            /
            <div class="total-time">{{ totalTime }}</div>
          </div>
          <button class="captions-btn" @click="toggleCaptions">
            <svg viewBox="0 0 24 24">
              <path fill="currentColor"
                    d="M18,11H16.5V10.5H14.5V13.5H16.5V13H18V14A1,1 0 0,1 17,15H14A1,1 0 0,1 13,14V10A1,1 0 0,1 14,9H17A1,1 0 0,1 18,10M11,11H9.5V10.5H7.5V13.5H9.5V13H11V14A1,1 0 0,1 10,15H7A1,1 0 0,1 6,14V10A1,1 0 0,1 7,9H10A1,1 0 0,1 11,10M19,4H5C3.89,4 3,4.89 3,6V18A2,2 0 0,0 5,20H19A2,2 0 0,0 21,18V6C21,4.89 20.1,4 19,4Z"/>
            </svg>
          </button>
          <button class="speed-btn wide-btn" @click="changePlaybackSpeed">
            {{ speed }}x
          </button>
          <button class="mini-player-btn" ref="mini" @click="toggleMiniMode">
            <svg viewBox="0 0 24 24">
              <path fill="currentColor" d="M21 3H3c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h18c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm0 16H3V5h18v14zm-10-7h9v6h-9z"/>
            </svg>
          </button>
          <button class="theater-btn" ref="theater" @click="toggleTheater">
            <svg class="tall" viewBox="0 0 24 24">
              <path fill="currentColor" d="M19 6H5c-1.1 0-2 .9-2 2v8c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V8c0-1.1-.9-2-2-2zm0 10H5V8h14v8z"/>
            </svg>
            <svg class="wide" viewBox="0 0 24 24">
              <path fill="currentColor" d="M19 7H5c-1.1 0-2 .9-2 2v6c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V9c0-1.1-.9-2-2-2zm0 8H5V9h14v6z"/>
            </svg>
          </button>
          <button class="full-screen-btn" ref="fullscreen" @click="toggleFullScreen">
            <svg class="open" viewBox="0 0 24 24">
              <path fill="currentColor" d="M7 14H5v5h5v-2H7v-3zm-2-4h2V7h3V5H5v5zm12 7h-3v2h5v-5h-2v3zM14 5v2h3v3h2V5h-5z"/>
            </svg>
            <svg class="close" viewBox="0 0 24 24">
              <path fill="currentColor" d="M5 16h3v3h2v-5H5v2zm3-8H5v2h5V5H8v3zm6 11h2v-3h3v-2h-5v5zm2-11V5h-2v5h5V8h-3z"/>
            </svg>
          </button>

        </div>
      </div>
      <video src="@/assets/my_iu.mp4" ref="video" @click="togglePlay" autoplay>
        <track kind="captions" srclang="en" src="assets/subtitles.vtt"></track>
      </video>
    </div>
  </div>
</template>

<script>

export default {
  name: 'Player',
  data() {
    return {
      paused: false,
      theaterMode: false,
      fullscreen : false,
      miniMode : false,
      volume: 1,
      currentTime: '0:00',
      totalTime: '',
      speed: 1,
      captions: {},
      previewImg: '',
      thumbnailImg: '',
      isScrubbing: false,
    }
  },
  mounted() {

    window.addEventListener('keyup', ((ev) => {
      const key = ev.key.toLowerCase()
      const tagName = window.document.activeElement.tagName.toLowerCase();
      if (tagName === "input") return;
      switch (key) {
        case " ": if(tagName === "button") return;
          break;
        case "t" : this.toggleTheater(); break;
        case "i": this.toggleMiniMode(); break;
        case "f": this.toggleFullScreen(); break;
        case "m": this.toggleMute(); break;
        case "arrowleft":
        case "j" :
          this.skip(-5); break;
        case "k": this.togglePlay(); break;
        case "arrowright":
        case "l":
          this.skip(5); break;
        case "c": this.toggleCaptions(); break;

        default: break;
      }
      if (ev.keyCode === 32) {
        this.togglePlay();
      }
    }));


    document.addEventListener("fullscreenchange", () => {
      this.$refs.video_container.classList.toggle('full-screen');
    });
    this.$refs.video.addEventListener('enterpictureinpicture', () => {
      this.$refs.video_container.classList.add("mini-player");
    });
    this.$refs.video.addEventListener('leavepictureinpicture', () => {
      this.$refs.video_container.classList.remove("mini-player");
    });

    this.$refs.video.addEventListener('loadeddata', () => {
      this.totalTime = this.formatDuration(this.$refs.video.duration);
    });
    this.$refs.video.addEventListener('timeupdate', () => {
      this.currentTime = this.formatDuration(this.$refs.video.currentTime);
      const percent = this.$refs.video.currentTime / this.$refs.video.duration;
      this.$refs.timelineContainer.style.setProperty("--progress-position", percent)
    });
    this.captions = this.$refs.video.textTracks[0];
    this.captions.mode = "hidden";

    this.$refs.timelineContainer.addEventListener("mousemove", this.handleTimelineUpdate);
    this.$refs.timelineContainer.addEventListener("mousedown", this.toggleScrubbing);
    document.addEventListener("mouseup", e => {
      if (this.isScrubbing) {  this.toggleScrubbing(e) ; }
    });
    document.addEventListener("mousemove", e => {
      if (this.isScrubbing) {  this.handleTimelineUpdate(e) ; }
    });
  },
  methods: {
    toggleScrubbing(e) {
      const rect = this.$refs.timelineContainer.getBoundingClientRect();
      const percent = Math.min(Math.max(0, e.x - rect.x), rect.width) / rect.width;
      this.isScrubbing = (e.buttons & 1) === 1;
      this.$refs.videoContainer.classList.toggle("scrubbing", this.isScrubbing);
      let wasPaused;
      if (this.isScrubbing) {
        wasPaused = this.$refs.video.paused;
        this.$refs.video.pause();
      } else {
        this.$refs.video.currentTime = percent * this.$refs.video.duration;
        if (!wasPaused) {
          this.$refs.video.play();
        }
      }

      this.handleTimelineUpdate(e)
    },
    handleTimelineUpdate(e) {
      const rect = this.$refs.timelineContainer.getBoundingClientRect();
      const percent = Math.min(Math.max(0, e.x - rect.x), rect.width) / rect.width
      const previewImgNumber = Math.max(
        1,
        Math.floor((percent * this.$refs.video.duration) / 10)
      )
      this.previewImg = `@/assets/previewImgs/preview${previewImgNumber}.jpg`;
      this.$refs.timelineContainer.style.setProperty('--percent', percent);

      if (this.isScrubbing) {
        e.preventDefault();
        this.thumbnailImg = this.previewImg;
        this.timeline.style.setProperty('--progress-position', percent)
      }
    },
    changePlaybackSpeed() {
      let newPlaybackRate = this.$refs.video.playbackRate + 0.25;
      if (newPlaybackRate > 2) {
        newPlaybackRate = 0.25;
      }
      this.$refs.video.playbackRate = newPlaybackRate;
      this.speed = newPlaybackRate;
    },
    toggleCaptions() {
      const isHidden = this.captions.mode === 'hidden';
      this.captions.mode = isHidden ? 'showing' : 'hidden';
      this.$refs.video_container.classList.toggle("captions", isHidden);
    },
    skip(duration) {
      this.$refs.video.currentTime += duration;
    },
    formatDuration(time) {
      const seconds = Math.floor(time % 60);
      const minutes = Math.floor(time / 60) % 60;
      const hours = Math.floor(time / 3600);
      const leadingZeroFormatter = new Intl.NumberFormat(undefined, {
        minimumIntegerDigits: 2,
      });
      if (hours === 0) {
        return `${minutes}:${leadingZeroFormatter.format(seconds)}`;
      } else {
        return `${hours}:${leadingZeroFormatter.format(minutes)}:${leadingZeroFormatter.format(seconds)}`;
      }
    },
    togglePlay() {
      if (this.$refs.video.paused) {
        this.paused = false;
        this.$refs.video.play();
      } else {
        this.paused = true;
        this.$refs.video.pause();
      }
    },
    // volume control
    toggleMute() {
      const videoContainer = this.$refs.video_container;
      const video = this.$refs.video;
      if (!video.muted) {
        this.volume = 0;
        videoContainer.dataset.volumeLevel = 'muted';
      } else {
        this.volume = video.volume;
        if (video.volume > .5) {
          videoContainer.dataset.volumeLevel = 'high';
        } else {
          videoContainer.dataset.volumeLevel = 'low';
        }
      }
      this.$refs.video.muted = !this.$refs.video.muted;
    },
    volumeChange(e) {
      this.value = e.target.value;
      this.$refs.video.volume = e.target.value;
      this.$refs.video.muted = e.target.value === 0;

      this.$refs.volumeSlider.value = this.$refs.video.volume;
      let volumeLevel;
      if (this.$refs.video.muted || this.$refs.video.volume === 0) {
        this.$refs.volumeSlider.value = 0;
        volumeLevel = "muted";
      } else if (this.$refs.video.volume > .5){
        volumeLevel = "high";
      } else {
        volumeLevel = "low";
      }

      this.$refs.video_container.dataset.volumeLevel = volumeLevel;

    },
    // view Mode
    toggleTheater() {
      this.$refs.video_container.classList.toggle('theater');
    },
    toggleFullScreen() {
      if (document.fullscreenElement == null) {
        this.$refs.video_container.requestFullscreen();
      } else {
        document.exitFullscreen();
      }
    },
    toggleMiniMode() {
      if (this.$refs.video_container.classList.contains('mini-player')) {
        document.exitPictureInPicture();
      } else {
        this.$refs.video.requestPictureInPicture();
      }
    },
  },
}
</script>

