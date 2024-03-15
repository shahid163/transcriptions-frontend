<script setup>
// Import styles.
import 'vidstack/player/styles/default/theme.css';
import 'vidstack/player/styles/default/layouts/audio.css';
import 'vidstack/player/styles/default/layouts/video.css';
// Register elements.
import 'vidstack/player';
import 'vidstack/player/layouts';
import 'vidstack/player/ui';

import { isHLSProvider} from 'vidstack';
import { onMounted, ref } from 'vue';

const emit = defineEmits(["timeUpdate"]);

const props = defineProps([
    "currentTime",
    "source",
])

const $player = ref()

onUnmounted(() => {
  
});

</script>

<template>
  <media-player
    class="player"
    title="Sprite Fight"
    :src="{src: props.source, type: props.source.type.replace(/\/.*/, '/object')}"
    crossorigin
    playsinline
    ref="$player"
    v-bind:currentTime="props.currentTime"
    v-on:time-update="emit('timeUpdate', $player.currentTime)"
    autoplay
  >
    <media-provider>
    </media-provider>
    <!-- Layouts -->
    <media-audio-layout />
    <media-video-layout />
  </media-player>
</template>

<style scoped>
.player {
  --brand-color: #f5f5f5;
  --focus-color: #4e9cf6;

  --audio-brand: var(--brand-color);
  --audio-focus-ring-color: var(--focus-color);
  --audio-border-radius: 2px;

  --video-brand: var(--brand-color);
  --video-focus-ring-color: var(--focus-color);
  --video-border-radius: 2px;

  /* 👉 https://vidstack.io/docs/player/components/layouts/default#css-variables for more. */
}

.player[data-view-type='audio'] media-poster {
  display: none;
}

.player[data-view-type='video'] {
  aspect-ratio: 16 /9;
}

.src-buttons {
  display: flex;
  align-items: center;
  justify-content: space-evenly;
  margin-top: 40px;
  margin-inline: auto;
  max-width: 300px;
}
</style>