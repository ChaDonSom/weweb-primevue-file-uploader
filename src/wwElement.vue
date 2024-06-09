<template>
  <div class="pv-file-uploader">
    <PVFileUpload
      name="demo[]"
      :multiple="true"
      accept="image/*"
      auto
      :maxFileSize="1000000"
      customUpload
      @uploader="onSelect"
    >
      <template #header="{ chooseCallback, uploadCallback, clearCallback }">
        <div class="flex flex-wrap justify-between items-center flex-1 gap-2">
          <div class="flex gap-2">
            <PVButton @click="chooseCallback()" icon="pi pi-images" rounded></PVButton>
            <!-- <PVButton
              @click="uploadEvent(uploadCallback)"
              icon="pi pi-cloud-upload"
              rounded
              severity="success"
              :disabled="!files || files.length === 0"
            ></PVButton> -->
            <PVButton
              @click="clearCallback()"
              icon="pi pi-times"
              rounded
              severity="danger"
              :disabled="!localFiles || localFiles.length === 0"
            ></PVButton>
          </div>
          <PVProgressBar
            :value="totalSizePercent"
            :showValue="false"
            :class="['md:w-[20rem] h-[1rem] w-full md:ml-auto', { 'exceeded-progress-bar': totalSizePercent > 100 }]"
            ><span class="white-space-nowrap">{{ totalSize }}B / 1Mb</span></PVProgressBar
          >
        </div>
      </template>
      <template>
        <div v-if="localFiles.length > 0">
          <h5>Pending</h5>
          <div class="flex flex-wrap p-0 sm:p-5 gap-5">
            <div
              v-for="(file, index) of localFiles"
              :key="file.name + file.type + file.size"
              class="card m-0 px-6 flex flex-col border surface-border items-center gap-3"
            >
              <div>
                <img role="presentation" :alt="file.name" :src="file.objectURL" width="100" height="50" />
              </div>
              <span class="font-semibold">{{ file.name }}</span>
              <div>{{ formatSize(file.size) }}</div>
              <PVBadge value="Pending" severity="warning" />
              <PVButton icon="pi pi-times" @click="onRemoveTemplatingFile(file, index)" rounded severity="danger" />
            </div>
          </div>
        </div>

        <div v-if="uploadedFiles.length > 0">
          <h5>Completed</h5>
          <div class="flex flex-wrap p-0 sm:p-5 gap-5">
            <div
              v-for="(file, index) of uploadedFiles"
              :key="file.name + file.type + file.size"
              class="card m-0 px-6 flex flex-col border surface-border items-center gap-3"
            >
              <div>
                <img role="presentation" :alt="file.name" :src="file.cdnUrl" width="100" height="50" />
              </div>
              <span class="font-semibold">{{ file.name }}</span>
              <div>{{ formatSize(file.size) }}</div>
              <PVBadge value="Completed" class="mt-3" severity="success" />
              <PVButton icon="pi pi-times" @click="removeUploadedFileCallback(index)" rounded severity="danger" />
            </div>
          </div>
        </div>
      </template>
      <template #empty>
        <div class="flex items-center justify-center flex-col">
          <i class="pi pi-cloud-upload border-2 border-circle p-5 text-8xl text-400 border-400 rounded-full" />
          <p class="mt-4 mb-0">Drag and drop files to here to upload.</p>
        </div>
      </template>
    </PVFileUpload>
  </div>
</template>

<script>
import PrimeVue from "primevue/config"
import FileUpload from "primevue/fileupload"
import Badge from "primevue/badge"
import Button from "primevue/button"
import ProgressBar from "primevue/progressbar"
import "primevue/resources/themes/aura-light-green/theme.css"
import "primeicons/primeicons.css"
import { UploadClient } from "@uploadcare/upload-client"
import { deleteFile } from "@uploadcare/rest-client"

export default {
  beforeCreate() {
    // Add Tailwind to the head
    if (!wwLib.getFrontDocument().getElementById("pv-tailwind")) {
      const script = wwLib.getFrontDocument().createElement("script")
      script.src = "https://cdn.tailwindcss.com"
      script.id = "pv-tailwind"
      wwLib.getFrontDocument().body.appendChild(script)
    }

    if (!this.$.appContext.app) return
    // If already registered, skip
    if (this.$.appContext.app.component("PVFileUpload")) return
    this.$.appContext.app.use(PrimeVue)
    this.$.appContext.app.component("PVFileUpload", FileUpload)
    this.$.appContext.app.component("PVBadge", Badge)
    this.$.appContext.app.component("PVButton", Button)
    this.$.appContext.app.component("PVProgressBar", ProgressBar)
  },
  props: {
    content: { type: Object, required: true },
  },
  data() {
    return {
      totalSize: 0,
      totalSizePercent: 0,
      /**
       * @type {UploadClient | null}
       */
      client: null,
      /**
       * @type {RestClient | null}
       */
      restClient: null,
      fileInput: null,
      localFiles: [],
      uploadedFiles: [],
    }
  },
  methods: {
    formatSize(size) {
      if (size === 0) return "0 B"
      const i = Math.floor(Math.log(size) / Math.log(1024))
      return `${(size / Math.pow(1024, i)).toFixed(2) * 1} ${["B", "KB", "MB", "GB", "TB"][i]}`
    },
    onRemoveTemplatingFile(file, index) {
      this.localFiles.splice(index, 1)
    },
    async removeUploadedFileCallback(index) {
      await deleteFile(this.uploadedFiles[index].uuid, { publicKey: this.content.publicKey })
    },
    uploadEvent() {},
    async onSelect(event) {
      const file = event.files[0]
      this.localFiles.push(file)
      const result = await this.client.uploadFile(new File([file], file.name), {
        publicKey: "6c7663f9a55af1ca85dd",
        store: "auto",
        metadata: {
          subsystem: "js-client",
          pet: "cat",
        },
      })
      console.log(result)
      this.localFiles.splice(this.localFiles.indexOf(file), 1)
      this.uploadedFiles.push(result)
    },
  },
  mounted() {
    this.client = new UploadClient({ publicKey: this.content.publicKey })
    // this.restClient = new RestClient({ publicKey: this.content.publicKey })
  },
}
</script>

<style scoped lang="scss"></style>