<template>
  <v-container fluid>

    <v-layout row wrap class='mb-10'>
      <v-flex xs12 sm6 offset-sm3>
        <v-card
          :color='getCardColor(1)'
          dark>
          <v-card-title>1. Select an Image
          </v-card-title>
          <v-layout row wrap>
            <v-flex xs10 offset-xs1>
              <v-file-input
                accept="image/png, image/jpeg, image/bmp"
                prepend-icon="mdi-camera"
                label="Select an image"
                @change="fileSelected">
              </v-file-input>
            </v-flex>
          </v-layout>
        </v-card>
      </v-flex>
    </v-layout>

    <v-layout row wrap class='mb-10'>
      <v-flex xs12 sm6 offset-sm3>
        <v-card dark :color='getCardColor(2)'>
          <v-card-title>2. Image Preview
          </v-card-title>
          <v-layout row wrap height='300'>
            <v-flex xs12 sm8 offset-sm2>
              <img :src="imageUrl" height="280"/>
            </v-flex>
          </v-layout>
          <v-layout row wrap>
            <v-flex xs10 sm6 offset-xs1 offset-sm3>
              <v-card-actions>
                <v-btn
                  :disabled="!imageSelected||loading||submitted"
                  dark
                  block
                  large
                  color="#886A08"
                  @click="getOutput"
                >
                  Submit
                </v-btn>
              </v-card-actions>
            </v-flex>
          </v-layout>
        </v-card>
      </v-flex>
    </v-layout>

    <v-layout row wrap class='mb-10'>
      <v-flex xs12 sm6 offset-sm3>
        <v-card dark :color='getCardColor(3)'>
          <v-card-title>3. Parsed Text
          </v-card-title>
          <v-card-text
            light>
            <v-layout row wrap>
              <v-flex xs10 sm8 offset-xs1 offset-sm2>
                <v-progress-linear
                  :value="loadingValue"
                  height="40"
                  striped
                  :active='loading'
                  >
                </v-progress-linear>
              </v-flex>
            </v-layout>
            <v-layout row wrap>
              <v-flex xs10 sm6 offset-xs1 offset-sm3>
                {{ output }}
              </v-flex>
            </v-layout>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>

    <v-overlay :value="loading">
      <v-progress-circular indeterminate size="64"></v-progress-circular>
    </v-overlay>

  </v-container>
</template>

<script>

import { TesseractWorker } from 'tesseract.js'

export default {
  data () {
    return {
      output: '',
      imageName: '',
      imageFile: '',
      imageUrl: '',
      loadingValue: '',
      loading: false,
      submitted: false,
      selected: false
    }
  },
  computed: {
    state () {
      if (!(this.imageName !== '' && this.imageFile !== '' && this.imageUrl !== '')) {
        return 1
      } else if (!this.submitted) {
        return 2
      } else {
        return 3
      }
    },
    imageSelected () {
      return this.imageName !== '' && this.imageFile !== '' && this.imageUrl !== ''
    }
  },
  methods: {
    getCardColor (value) {
      if (this.state === value) {
        return '#033'
      } else {
        return '#1C1C1C'
      }
    },
    getOutput () {
      const worker = new TesseractWorker()
      const myImage = this.imageUrl
      worker.recognize(myImage)
        .progress(progress => {
          if (progress.status === 'recognizing text') {
            this.loadingValue = progress.progress * 100
          }
          this.submitted = true
          this.loading = true
          this.output = progress.status
        }).then(result => {
          this.output = result.text
          this.loading = false
        })
    },
    fileSelected (e) {
      this.submitted = false
      this.output = ''
      const file = e
      if (e !== undefined && e !== null) {
        this.selected = true
        this.imageName = file.name
        if (this.imageName.lastIndexOf('.') <= 0) {
          return
        }
        const fr = new FileReader()
        fr.readAsDataURL(file)
        fr.addEventListener('load', () => {
          this.imageUrl = fr.result
          this.imageFile = file // this is an image file that can be sent to server...
        })
      } else {
        this.imageName = ''
        this.imageFile = ''
        this.imageUrl = ''
        this.selected = false
      }
    }
  }
}
</script>

<style>
.mb-10{
  margin-bottom: 10px !important;
}
.imgHolder{
  height: 300px;
}
</style>
