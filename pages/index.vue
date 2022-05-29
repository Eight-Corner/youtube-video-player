<template>
  <div>
    <h1 style="text-align: center;"> YOUTUBE - VIDEO - PLAYER </h1>
    <div class="video-container">
      <div class="video-controls-container">
        <div class="timeline-container"></div>
          <div class="controls">
            <button class="play-pause-btn">
              <svg class="play-icon" height="100%" version="1.1" viewBox="0 0 36 36" width="100%"><use class="ytp-svg-shadow" xlink:href="#ytp-id-935"></use><path class="ytp-svg-fill" d="M 12,26 18.5,22 18.5,14 12,10 z M 18.5,22 25,18 25,18 18.5,14 z" id="ytp-id-935"></path></svg>
              <svg class="pause-icon" height="100%" version="1.1" viewBox="0 0 36 36" width="100%"><use class="ytp-svg-shadow" xlink:href="#ytp-id-974"></use><path class="ytp-svg-fill" d="M 12,26 16,26 16,10 12,10 z M 21,26 25,26 25,10 21,10 z" id="ytp-id-974"></path></svg>
            </button>
            <button class="play-pause-btn">
              <svg height="100%" version="1.1" viewBox="0 0 36 36" width="100%"><use class="ytp-svg-shadow" xlink:href="#ytp-id-12"></use><path class="ytp-svg-fill" d="M 12,24 20.5,18 12,12 V 24 z M 22,12 v 12 h 2 V 12 h -2 z" id="ytp-id-12"></path></svg>
            </button>
          </div>
      </div>
      <video src="@/assets/my_iu.mp4"></video>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage'
}
</script>

