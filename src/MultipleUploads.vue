<template>
  <form enctype="multipart/form-data" @submit.prevent="sendFile">
    <div v-if="message" :class="`message ${error ? 'is-danger' : 'is-success'}`">
      <div class="message-body">{{message}}</div>
    </div>

    <div class="field">
      <div class="file is-boxed is-warning">
        <label class="file-label">
          <input
            multiple
            type="file"
            class="file-input"
            name="file"
            id="file"
            ref="files"
            @change="selectFile"
          >
          <span class="file-cta">
            <span class="file-icon">
              <i class="fas fa-upload"></i>
            </span>
            <span class="file-label">Choose a file...</span>
          </span>
        </label>
      </div>
    </div>
    <div class="field">
      <div
        v-for="(file, index) in files"
        :key="index"
        :class="`level ${file.invalidMessage && 'has-text-danger'}`"
      >
        <div class="level-left">
          <div class="level-item">
            {{file.name}}
            <span v-if="file.invalidMessage">&nbsp;- {{file.invalidMessage}}</span>
          </div>
        </div>
        <div class="level-right">
          <div class="level-item">
            <a @click.prevent="files.splice(index, 1);uploadfiles.splice(index, 1)" class="delete"></a>
          </div>
        </div>
      </div>
    </div>
    <div class="field">
      <button class="button is-info">Send</button>
    </div>
  </form>
</template>

<script>
import axios from "axios";
import _ from "lodash";

export default {
  name: "MultipleUploads",
  data() {
    return {
      files: [],
      uploadfiles: [],
      message: "",
      error: false
    };
  },
  methods: {
    selectFile() {
      const files = this.$refs.files.files;
      this.uploadfiles = [...this.files, ...files];

      this.files = [
        ...this.files,
        ..._.map(files, file => ({
          name: file.name,
          size: file.size,
          type: file.type,
          invalidMessage: this.validate(file)
        }))
      ];
    },

    validate(file) {
      const MAX_SIZE = 200000;
      const allowedTypes = ["image/jpeg", "image/png", "image/gif"];

      if (file.size > MAX_SIZE) {
        return `Max size: ${MAX_SIZE / 1000}Kb`;
      }

      if (!allowedTypes.includes(file.type)) {
        return "Not an image";
      }

      return "";
    },

    async sendFile() {
      const formData = new FormData();
      _.forEach(this.uploadfiles, file => {
        if (this.validate(file) === "") {
          formData.append("files", file);
        }
      });

      try {
        await axios.post("/multiple", formData);
        this.message = "Files has been sent";
        this.files = [];
        this.uploadfiles = [];
      } catch (err) {
        this.message = err.response.data.error;
        this.error = true;
      }
    }
  }
};
</script>

<style scoped>
</style>