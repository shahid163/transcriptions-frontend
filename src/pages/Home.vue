<script setup>
import { ref } from "vue";
import WelcomeBanner from '../partials/dashboard/WelcomeBanner.vue'
import UploadMedia from '../partials/home/UploadMedia.vue'
import Transcription from '../partials/home/Transcription.vue'
import Player from "../components/player/Player.vue";

const transcriptionId = ref(null)
const playerSource = ref(null)
const showPlayer = ref(false)
const showTranscription = ref(false)
const playerCurrentTime = ref(0)
const updatedTime = ref(0)

function onMediaUploaded(id, file) {
  transcriptionId.value = id
  playerSource.value = file
  showTranscription.value = true
}

function onFileUploadReady() {
  showPlayer.value = false
  showTranscription.value = false
}

</script>

<template>
  <div class="flex h-screen overflow-hidden">
    <!-- Content area -->
    <div class="relative flex flex-col flex-1 overflow-y-auto overflow-x-hidden">

      <main>
        <div class="px-4 sm:px-6 lg:px-8 py-8 w-full max-w-9xl mx-auto">
          <!-- Welcome banner -->
          <WelcomeBanner />
          
          <UploadMedia
            v-on:file-uploaded="onMediaUploaded"
            v-on:file-upload-ready="onFileUploadReady"
          />

          <Transcription 
            v-if="showTranscription"
            v-bind:id="transcriptionId"
            v-bind:updated-time="updatedTime"
            v-on:loaded="showPlayer = true"
            v-on:seekMedia="(timestamp) => playerCurrentTime = timestamp"
          />

          <Player
            v-if="showPlayer"
            v-bind:source="playerSource"
            v-on:timeUpdate="(timestamp) => updatedTime = timestamp"
            v-bind:currentTime="playerCurrentTime"
          />

        </div>
      </main>

    </div> 

  </div>
</template>