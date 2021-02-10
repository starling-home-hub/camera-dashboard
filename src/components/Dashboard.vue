<template>
  <div class="main">
    <div v-for="camera in cameras" :key="camera.id">
      <img v-show="camera.image" :src="camera.image">
      <div class="camera-name">{{ camera.name }}</div>
      <div class="last-updated">{{ camera.lastUpdated }}</div>
    </div>
  </div>
</template>

<script>
  const HUB_IP = 'X.X.X.X';
  const HUB_API_KEY = 'put_your_API_key_here';

  const UPDATE_INTERVAL_SECONDS = 10;

  const axios = require('axios');

  export default {
    data() {
      return {
        cameras: [],
        updateTimer: null
      }
    },
    async mounted() {
      await this.getCameras();
      await this.updateCameras();
      this.updateTimer = setInterval(() => this.updateCameras(), UPDATE_INTERVAL_SECONDS * 1000);
    },
    beforeDestroy() {
      clearInterval(this.updateTimer);
    },
    methods: {
      async getCameras() {
        let devices = (await axios.get(this.makeEndpoint('devices'))).data;
        if (devices.status != 'OK') {
          console.log('Couldn\'t retrieve Nest devices.');
          return;
        }

        let cameras = devices.devices.filter(device => device.type == 'cam');
        if (cameras.length == 0) {
          console.log('No cameras found.');
          return;
        }

        this.cameras = cameras.map(camera => {
          return {
            id: camera.id,
            name: ((camera.where || '') + ' ' + (camera.name || '')).trim(),
            lastUpdated: 'Loading ...',
            image: null
          };
        });
      },
      async updateCameras() {
        for (let camera of this.cameras) {
          try {
            let snapshot = (await axios.get(this.makeEndpoint('devices/' + camera.id + '/snapshot'), { responseType: 'arraybuffer' })).data;
            let base64String = btoa(String.fromCharCode(...new Uint8Array(snapshot)));
            camera.lastUpdated = new Date().toLocaleString();
            camera.image = 'data:image/jpeg;base64,' + base64String;
          } catch(error) {
            console.log('Warning: unable to update camera', camera.id);
          }
        }
      },
      makeEndpoint(endpoint, query) {
        return 'http://' + HUB_IP + ':3080/api/connect/v1/' + endpoint + '?key=' + HUB_API_KEY + (query ? '&' + query : '');
      }
    }
  }
</script>

<style scoped>
  .main {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    grid-gap: 1rem;
  }

  .main > div {
    background: #46a9dd;
    padding: 1rem;
    display: grid;
    place-items: center;
    border-radius: 16px;
  }

  .main > div::before {
    content: "";
    display: block;
    padding-bottom: 75%;
    grid-area: 1/1/2/2;
  }

  .main > div img {
    width: 100%;
    grid-area: 1/1/2/2;
  }

  .camera-name, .last-updated {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }

  .camera-name {
    margin-top: 8px;
    font-weight: 500;
  }
</style>
