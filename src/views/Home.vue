<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Blank</ion-title>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Blank</ion-title>
        </ion-toolbar>
      </ion-header>
    
      <div class="ion-text-center ion-margin">
        <ion-button @click="prompt">Prompt</ion-button>
        <ion-button @click="fromCamera">From Camera</ion-button>
        <ion-button @click="fromPhotos">From Photos</ion-button>
        <template v-if="hasImage">
          <div><img ref="image"></div>
          <div><ion-button @click="removeImage">Remove</ion-button></div>
        </template>
      </div>
    </ion-content>
  </ion-page>
</template>


<script lang="ts">

import { defineComponent, nextTick } from 'vue';
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonButton } from '@ionic/vue';
import { Plugins, CameraResultType, CameraSource } from '@capacitor/core';

const { Camera } = Plugins;

export default defineComponent({
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonButton,
  },

  data() {
    return {
      hasImage: false,
    };
  },

  methods: {
    async prompt() {
      await this.getPhoto(CameraSource.Prompt);
    },

    async fromCamera() {
      await this.getPhoto(CameraSource.Camera);
    },

    async fromPhotos() {
      await this.getPhoto(CameraSource.Photos);
    },

    async getPhoto(source: CameraSource) {
      try {
        const photo = await Camera.getPhoto({
          resultType: CameraResultType.Uri,
          source,
        });

        if (photo.webPath) {
          this.hasImage = true;
          await nextTick();
          
          const image = this.$refs.image as HTMLImageElement;
          image.src = photo.webPath;
        }
        else {
          this.hasImage = false;
        }
      }
      catch (error) {
        console.error(error);
      }
    },

    removeImage() {
      this.hasImage = false;
    },
  },
});

</script>
