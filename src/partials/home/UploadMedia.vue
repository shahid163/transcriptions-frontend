<script setup>
import { ref } from "vue";

const emit = defineEmits(['fileUploadReady', 'fileUploaded'])

const isUploading = ref(false),
  uploadProgress = ref(0)

async function onFileChange(e) {
  const files = e.target.files;
  if(!files.length) return;

  emit("fileUploadReady")
  const file = files[0];

  const xhr = new XMLHttpRequest();
  isUploading.value = true;

  xhr.upload.addEventListener('progress', function(event) {
    if (event.lengthComputable) {
      const progress = Math.round((event.loaded * 100) / event.total);
      uploadProgress.value = progress;
    } else {
      progressDiv.textContent = 'Upload progress: Unknown';
      uploadProgress.value = 50;
    }
  });

  xhr.addEventListener("loadend", () => {
    isUploading.value = false;
    uploadProgress.value = 0; // Setting to intial Value

    const transcriptionId = JSON.parse(xhr.responseText)?.id

    emit('fileUploaded', transcriptionId, file)
  });
  
  xhr.open('PUT', `http://127.0.0.1:8000/upload/${file.name}`);
  xhr.setRequestHeader('Accept', 'application/json');
  xhr.send(file);
}
</script>

<template>
    <div class="flex flex-col items-end gap-4 mb-8">
        <div v-if="isUploading" class="w-60">
            <!-- Progress -->
            <div class="flex w-full h-1.5 bg-gray-200 rounded-full overflow-hidden dark:bg-gray-700" role="progressbar" aria-valuenow="25" aria-valuemin="0" aria-valuemax="100">
              <div class="flex flex-col justify-center rounded-full overflow-hidden bg-blue-600 text-xs text-white text-center whitespace-nowrap transition duration-500 dark:bg-blue-500" :style="{width: uploadProgress + '%'}"></div>
            </div>
            <!-- End Progress -->
        </div>
        
        <input hidden accept="audio/*,video/*" type="file" name="file" id="file" v-on:change="onFileChange">
        
        <label for="file" class="btn bg-indigo-500 hover:bg-indigo-600 text-white cursor-pointer">  
            <svg v-if="isUploading" aria-hidden="true" class="w-4 h-4 text-gray-200 animate-spin dark:text-gray-600 fill-blue-600" viewBox="0 0 100 101" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z" fill="currentColor"/>
                <path d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z" fill="currentFill"/>
            </svg>
            <svg v-else class="w-4 h-4 fill-current opacity-50 shrink-0" viewBox="0 0 16 16">
                <path d="M15 7H9V1c0-.6-.4-1-1-1S7 .4 7 1v6H1c-.6 0-1 .4-1 1s.4 1 1 1h6v6c0 .6.4 1 1 1s1-.4 1-1V9h6c.6 0 1-.4 1-1s-.4-1-1-1z" />
            </svg>
            <span class="hidden xs:block ml-2">Upload{{ isUploading ? "ing" : ""}}</span>
        </label>
    </div>
</template>