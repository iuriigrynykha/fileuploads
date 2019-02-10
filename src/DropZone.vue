<template>
  <form enctype="multipart/form-data" @submit.prevent="sendFile">
    <div v-if="message" :class="`message ${error ? 'is-danger' : 'is-success'}`">
      <div class="message-body">{{message}}</div>
    </div>

    <div class="dropzone">
      <input type="file" class="input-file" ref="file" @change="sendFile">

      <p v-if="!uploading" class="call-to-action">Drag your files here</p>
      <p v-if="uploading" class="progress-bar">
        <progress class="progress is-primary" :value="progress" max="100">{{progress}} %</progress>
      </p>
    </div>
    <div class="content">
      <div class="column is-multiline">
        <div v-for="file in uploadedFiles" :key="file" class="column is-4">
          <figure class="image">
            <img :src="file" alt>
          </figure>
        </div>
      </div>
    </div>
  </form>
</template>

<script>
import axios from "axios";
export default {
  name: "DropZone",
  data() {
    return {
      file: "",
      message: "",
      error: false,
      uploading: false,
      uploadedFiles: [],
      progress: 0
    };
  },
  methods: {
    async sendFile() {
      const file = this.$refs.file.files[0];
      const formData = new FormData();
      formData.append("file", file);

      try {
        this.uploading = true;
        const res = await axios.post("/dropzone", formData, {
          onUploadProgress: e =>
            (this.progress = Math.round((e.loaded * 100) / e.total))
        });
        this.uploadedFiles.push(res.data.file);
        this.uploading = false;
      } catch (err) {
        this.message = err.response.data.error;
        this.error = true;
        this.uploading = false;
      }
    }
  }
};
</script>

<style scoped>
.dropzone {
  min-height: 200px;
  padding: 10px 10px;
  position: relative;
  cursor: pointer;
  outline: 2px dashed grey;
  outline-offset: -10px;
  background: lightcyan;
  color: dimgray;
}

.input-file {
  opacity: 0;
  width: 100%;
  height: 200px;
  position: absolute;
  cursor: pointer;
}

.dropzone:hover {
  background: lightblue;
}

.dropzone .call-to-action {
  font-size: 1.2em;
  text-align: center;
  padding: 70px 0;
}

.dropzone .progress-bar {
  text-align: center;
  padding: 70px 10px;
}
</style>