<template>
  <div>
    <video
      ref="videoRef"
      style="width: 100%; aspect-ratio: 16/9"
      controls
      autoplay
      muted
    >
      <source
        src="http://playertest.longtailvideo.com/adaptive/wowzaid3/playlist.m3u8"
        type="application/x-mpegURL"
      />
    </video>
  </div>
</template>
<script setup lang="ts">
import Hls from 'hls.js';

const videoRef = ref(null);

watch(videoRef, () => {
  if (!videoRef.value) {
    return;
  }

  const sources = videoRef.value.getElementsByTagName('source');
  if (sources.length === 0) {
    return;
  }

  const hlsUrl = sources[0].src;
  if (videoRef.value.canPlayType('application/vnd.apple.mpegurl')) {
    videoRef.value.src = hlsUrl;
  } else if (Hls.isSupported()) {
    console.log('supported');
    const hls = new Hls({
      autoStartLoad: true,
    });

    hls.loadSource(hlsUrl);
    hls.attachMedia(videoRef.value);
    if (videoRef.value.autoplay) {
      console.log('autoplay');
      console.log(hls);
      hls.on('hlsManifestParsed', function () {
        console.log('manifest parsed');
        videoRef.value?.play();
      });
    }
  }
});
</script>
