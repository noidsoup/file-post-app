<template>
  <div id="app">
    <v-app id="inspire">
      <v-layout>
        <div class="container">
          <div>
            <label>
              Files
              <input
                type="file"
                id="files"
                ref="files"
                multiple
                v-on:change="handleFilesUpload()"
              />
            </label>
          </div>
          <div>
            <div v-for="(file, key) in files" class="file-listing">
              {{ file.name }}
              <span class="remove-file" v-on:click="removeFile( key )">Remove</span>
            </div>
          </div>
          <br />
          <div>
            <v-btn v-on:click="addFiles()">Add Files</v-btn>
          </div>
          <br />
          <div>
            <v-btn color="primary" v-on:click="submitFiles()">Submit</v-btn>
          </div>
          <v-alert v-if="error === true" :value="true" type="error">{{errorMsg}}</v-alert>
        </div>
      </v-layout>
    </v-app>
  </div>
</template>

<script>
import axios from "axios";

export default {
  /*
      Defines the data used by the component
    */
  data() {
    return {
      files: [],
      error: false,
      errorMsg: null
    };
  },

  /*
      Defines the method used by the component
    */
  methods: {
    /*
        Adds a file
      */
    addFiles(){
      this.$refs.files.click();
    },

    /*
        Submits files to the server
      */
    submitFiles() {
      /*
          Initialize the form data
        */
      let formData = new FormData();

      /*
          Iteate over any file sent over appending the files
          to the form data.
        */
      for (var i = 0; i < this.files.length; i++) {
        let file = this.files[i];

        formData.append("files[" + i + "]", file);
      }

      /*
          Make the request to the POST URL
        */
      axios
        .post("/the_server_endpoint", formData, {
          headers: {
            "Content-Type": "multipart/form-data"
          }
        })
        .then(() => {
          this.files = "";
          console.log("SUCCESS!!");
        })
        .catch(err => {
          this.error = true;
          this.errorMsg = err;
          console.log("FAILURE!!", err);
        });
    },

    /*
        Handles the uploading of files
      */
    handleFilesUpload() {
      let uploadedFiles = this.$refs.files.files;

      /*
          Adds the uploaded file to the files array
        */
      for (var i = 0; i < uploadedFiles.length; i = i + 1) {
        this.files.push(uploadedFiles[i]);
      }
    },

    /*
        Removes a select file the user has uploaded
      */
    removeFile(key) {
      this.files.splice(key, 1);
    },
  },
};
</script>

<style>
input[type="file"] {
  position: absolute;
  top: -500px;
}

div.file-listing {
  width: 200px;
}

span.remove-file {
  color: red;
  cursor: pointer;
  float: right;
}
</style>
