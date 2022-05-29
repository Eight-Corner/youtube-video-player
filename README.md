# 도전과제

# Nuxt로 Youtube Video Player 개발하기

---

```
auth : corner
date : 05-29
```



## 목차

- [들어가며](##들어가며)
  - [Homebrew로 FFmpeg설치하기](###Homebrew로 FFmpeg설치하기)
  - [Video와 Preview 이미지 준비하기](###Video와 Preview 이미지 준비하기)

- [환경 설치](##환경설치)
- 준비 파일 : 한 개의 video 영상
- [Video Player HTML/CSS](##Video Player HTML/CSS)

---

## 들어가며

> 💡 HTML, Javascript로 구성하는 Youtube-Player를 Nuxt.js로 개발하는 방법을 제시하고 있습니다.
>
> 글쓴이의 운영체제는 Mac OS이므로 이를 감안하시어 읽어주시길 바랍니다.



---



## 환경설치

맥에서 Homebrew를 이용하여 ffmpeg 설치를 합니다.

> 💡 Homebrew가 설치되어있지 않다면 [Mac M1 Homebrew InstallGuide](https://velog.io/@corner3499/Mac-M1-Homebrew-Install-Guide-%EC%84%A4%EC%B9%98-%ED%95%B4%EA%B2%B0%EA%B3%BC%EC%A0%95-wyhfgtuh)의 문서를 참조하세요.



### Homebrew로 FFmpeg설치하기

다음 명령어를 실행하면 바로 FFmpeg 설치가 됩니다. 의존 패키지가 많아 시간이 많이 소요됩니다.

intel `brew install ffmpeg`

m1 `arch -arm64 brew install ffmpeg`

```bash
$ brew install ffmpeg 
or  ||
$ arch -arm64 brew install ffmpeg
...
==> Auto-updated Homebrew!
...
==> New Formulae
...
```

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h2pfimo9v9j218x0u0n5d.jpg" width="70%" alt="">

FFmpeg을 설치하고 ffmpeg을 인자없이 실행하면 버전, 빌드 옵션, 기본적인 사용 방법이 출력됩니다.

```bash
$ ffmpeg
ffmpeg version 5.0 Copyright ....
```

### Video 영상 준비하기



프로젝트 폴더에서 `assets/` 경로를 생성하고 Video영상과 `previewImgs/` 경로에 영상 중간중간을 캡처해서 미리보기가 될 스크린샷을 넣어둡니다.

영상을 넣어두고 ffmpeg 명령어를 이용하여 해당 비디오의 preview 이미지를 생성합니다.

영상 길이의 프레임 1/10 만큼 썸네일을 찍는다는 것인데 저의 영상은 6초짜리라서 1장의 썸네일만 생성되었습니다.

```bash
$ ffmpeg -i assets/my_video.mp4 -vf fps=1/10,scale=120:-1 assets/previewImgs/preview%d.jpg
```

명령어를 수행하고 난 뒤 터미널에서 작업에 대한 내용이 출력됩니다.

```bash
[swscaler @ 0x109030000] [swscaler @ 0x148d68000] deprecated pixel format used, make sure you did set range correctly
...
```

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h2pfogzr2ij20xm03adg3.jpg" alt="" width="80%"/>최종구조



---

## Video Player HTML/CSS

Nuxt에서 HTML과 CSS를 작업하기 위해 index.vue로 이동합니다.

```vue
<template>
  <div>
    <h1> YOUTUBE - VIDEO - PLAYER </h1>
    <video src="@/assets/my_video.mp4"></video>
  </div>
</template>

<script>
export default {
  name: 'IndexPage'
}
</script>
```

video 태그에 assets 경로로 넣은 비디오를 넣고 서버를 실행해서 영상이 뜨는지 확인해봅니다.



우리는 간단한 style을 이용해서 video를 youtube처럼 화면 사이즈에따라 줄어들고 커지는 것 처럼 반응하도록 작업하겠습니다.

```vue
<template>
  <div>
    <h1> YOUTUBE - VIDEO - PLAYER </h1>
    <div class="video-container">
      <video src="@/assets/my_video.mp4"></video>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage'
}
</script>

<style scoped>
*, *::before, *::after {
  box-sizing: border-box;
}

body {
  margin: 0;
}

.video-container {
  width: 90%;
  max-width: 1000px;
  display: flex;
  justify-content: center;
  margin-inline: auto;
}
video {
  width: 100%;
  height: auto;
}
</style>
```

video 태그를 `<div class='video-container'>`로 감싸고 , 위 코드처럼 스타일을 적용했을 때 사이즈가 적절히 변하는지 확인해봅니다.



저는 `<video src="..." controls>` 태그에 controls 속성을 추가했습니다.

![image-20220529193107224](https://tva1.sinaimg.cn/large/e6c9d24egy1h2pgdo1uqjj216e0qctcw.jpg)



하지만 우리는 유튜브 같은 컨트롤을 이용할 것이기 때문에 수정을 해야합니다.



우선 template 코드를 아래와 같이 수정합니다.

```html
 <div class="video-container">
      <div class="video-controls-container">
        <div class="timeline-container"></div>
          <div class="controls">
            <button class="play-pause-btn">
                Play
            </button>
          </div>
      </div>
      <video src="@/assets/my_iu.mp4"></video>
</div>
```



css를 아래와 같이 수정합니다.

```css
.video-container {
  width: 90%;
  max-width: 1000px;
  display: flex;
  justify-content: center;
  margin-inline: auto;
  position: relative;
}
video {
  width: 100%;
}
.video-controls-container {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  color: white;
  z-index: 100;
  opacity: 0;
  transition: opacity 150ms ease-in-out;
}
.video-container:hover .video-controls-container,
.video-container:focus-within .video-controls-container,
.video-container.paused .video-controls-container {
  opacity: 1;
}

.video-controls-container .controls {
  display: flex;
  gap: .5rem;
  padding: .25rem;
  align-items: center;
}

.video-controls-container .controls button {
  background: none;
  border: none;
  color: inherit;
  padding: 0;
  /*height: 30px;*/
  width: 30px;
  font-size: 1.1rem;
}

.video-controls-container .controls {
  display: flex;
  gap: .5rem;
  padding: .25rem;
  align-items: center;
}

```

영상과 컨트롤러에 마우스가 올려지거나, 재생중일 때, 클릭했을 때 컨트롤이 자연스럽게 보여지도록 하는 transition 애니메이션 처리도 해줍니다.





이제 컨트롤바에서 Play버튼을 유튜브 아이콘 처럼 변경해야합니다. 저는 유튜브에서 개발자모드로 소스를 찍어서 svg 코드를 가져왔습니다.

임의 아이콘을 사용하여도 좋지만, 어렵거나 큰 문제도 아니고 간편하게 하기 위해 유튜브를 모방하는 것이 목적이기 때문에 그렇게 했습니다.



```html
  <button class="play-pause-btn">
              <svg class="play-icon" height="100%" version="1.1" viewBox="0 0 36 36" width="100%"><use class="ytp-svg-shadow" xlink:href="#ytp-id-935"></use><path class="ytp-svg-fill" d="M 12,26 18.5,22 18.5,14 12,10 z M 18.5,22 25,18 25,18 18.5,14 z" id="ytp-id-935"></path></svg>
              <svg class="pause-icon" height="100%" version="1.1" viewBox="0 0 36 36" width="100%"><use class="ytp-svg-shadow" xlink:href="#ytp-id-974"></use><path class="ytp-svg-fill" d="M 12,26 16,26 16,10 12,10 z M 21,26 25,26 25,10 21,10 z" id="ytp-id-974"></path></svg>
</button>
<button class="play-pause-btn">
              <svg height="100%" version="1.1" viewBox="0 0 36 36" width="100%"><use class="ytp-svg-shadow" xlink:href="#ytp-id-12"></use><path class="ytp-svg-fill" d="M 12,24 20.5,18 12,12 V 24 z M 22,12 v 12 h 2 V 12 h -2 z" id="ytp-id-12"></path></svg>
</button>
```

컨트롤 클래스 태그 아래 버튼 태그에 svg를 가져오거나, 아이콘을 넣습니다.

```css
.video-controls-container .controls button {
  background: none;
  border: none;
  color: inherit;
  padding: 0;
  /*height: 30px;*/
  width: 30px;
  font-size: 1.1rem;
}

.video-container.paused .pause-icon {
  display: none;
}
.video-container:not(.paused) .play-icon {
  display: none;
}
```

스타일을 적용해줍니다.



<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h2pk0kixf5j21i30u0q3t.jpg" width="80%" alt="" />

영상이 재생중이면 일시정지 버튼을, 일시정지 상태면 재생버튼이 뜨도록 처리할 것입니다.

현재는 `<div class="video-container">`이렇게 paused 클래스가 없을 땐 일시정지를, paused 클래스를 넣으면 재생 버튼이 뜨는 상태가 되도록 css 처리만 하였습니다.



`.video-controls-container .contorls button {}`css 코드에 추가합니다.

```css
cursor: pointer;
opacity: .85;
transition: opacity 150ms ease-in-out;
```

그리고 위 코드 아래에 컨트롤 버튼위에 올려질 경우 버튼이 보여지도록 새 css를 넣습니다.

```css
.video-controls-container .controls button:hover {
  opacity: 1;
}
```



[전체코드는 코너의 Github](https://github.com/eight-corner/youtube-video-player)

---







