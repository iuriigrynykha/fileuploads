<template>
  <div class="section">
    <div class="container">
      <div class="columns">
        <div class="column">
          <simple-upload/>
        </div>
        <div class="column">
          <multiple-uploads/>
        </div>
      </div>

      <drop-zone/>
    </div>
  </div>
</template>

<script>
import SimpleUpload from "./SimpleUpload";
import MultipleUploads from "./MultipleUploads";
import DropZone from "./DropZone";

export default {
  name: "app",
  components: { SimpleUpload, MultipleUploads, DropZone }
};
</script>
