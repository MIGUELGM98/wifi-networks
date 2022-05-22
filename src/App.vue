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

      <div id="container">
        <p>Wifi</p>
        <ion-button @click="logCurrentNetworkStatus"> Get Current Connection </ion-button>
        <p>{{ status }}</p>
        <p>BSSID: {{ wifiBssid }}</p>
        <p>NAME OF WIFI: {{ wifiSsid }}</p>


        <ion-button @click="wifiOff">GET WIFI NETWORKS</ion-button>
        <div v-for="item in scan" :key="item.id">
          <p>{{item.SSID}}</p>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
  IonButton,
} from "@ionic/vue";
import { defineComponent, ref, onMounted } from "vue";
import { Network } from "@capacitor/network";
import { WifiWizard2 } from "@awesome-cordova-plugins/wifi-wizard-2";
export default defineComponent({
  name: "HomePage",
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonButton,
  },
  setup() {
    const data = ref();
    const wifiSsid = ref();
    const wifiBssid = ref();
    const scan = ref();
    Network.addListener("networkStatusChange", (status) => {
      console.log("Network status changed", status);
    });
    const wifiOff = async ()=> {
      scan.value = await WifiWizard2.scan();
    }
    const status = ref();
    const logCurrentNetworkStatus = async () => {
      status.value = await Network.getStatus();
      console.log("Network status:", status.value);
    };

    onMounted(async() => {
      wifiSsid.value = await WifiWizard2.getConnectedSSID();
      wifiBssid.value = await WifiWizard2.getConnectedBSSID();
      console.log(wifiSsid.value);
      console.log(wifiBssid.value);

      data.value = await WifiWizard2.listNetworks();

      console.log(WifiWizard2.listNetworks());
    });
    return {
      logCurrentNetworkStatus,
      status,
      data,
      wifiSsid,
      wifiBssid,
      scan,wifiOff
    };
  },
});
</script>

<style scoped>
#container {
  text-align: center;

  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;

  color: #8c8c8c;

  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>
