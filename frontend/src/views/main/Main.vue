<template>
  <v-main class="fill-height vertical-container">
    <v-navigation-drawer
        dark
        fixed
        app
        persistent
        class="background"
        mobile-breakpoint="sm"
        width="72"
    >
      <v-layout column fill-height>
        <v-list subheader class="align-center">
          <v-list-item to="/">
            <v-list-item-content>
              <div class="align-center text-center">
                <img src="@/assets/logo_v2_white.png" alt="Giskard icon" width="45px"/>
              </div>
            </v-list-item-content>
          </v-list-item>
          <v-divider/>
          <v-list-item to="/main/projects">
            <v-list-item-content>
              <v-icon>web</v-icon>
              <div class="caption">Projects</div>
            </v-list-item-content>
          </v-list-item>
        </v-list>
        <v-spacer></v-spacer>
        <v-list>
          <v-list-item>
            <v-list-item-content v-if="warningMessage">
              <v-tooltip right>
                <template v-slot:activator="{ on, attrs }">
                  <v-icon color="orange" v-bind="attrs" v-on="on">mdi-alert</v-icon>
                </template>
                <span>{{ warningMessage }}</span>
              </v-tooltip>
            </v-list-item-content>
          </v-list-item>
          <v-divider/>
          <v-list-item v-show="hasAdminAccess" to="/main/admin/">
            <v-list-item-content>
              <v-icon>mdi-cog</v-icon>
              <div class="caption">Settings</div>
            </v-list-item-content>
          </v-list-item>
          <v-divider/>
          <v-list-item to="/main/profile/view" v-if="authAvailable">
            <v-list-item-content>
              <v-icon>person</v-icon>
              <div class="caption">{{ userId }}</div>
            </v-list-item-content>
          </v-list-item>
          <v-list-item @click="logout" v-if="authAvailable">
            <v-list-item-content>
              <v-icon>logout</v-icon>
              <div class="caption">Logout</div>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-layout>
    </v-navigation-drawer>

    <div class="pa-1 vertical-container">
      <router-view></router-view>
    </div>
  </v-main>
</template>

<script lang="ts" setup>
import {useUserStore} from "@/stores/user";
import {useMainStore} from "@/stores/main";
import {computed, ref} from "vue";
import moment from "moment/moment";

const mainStore = useMainStore();
const userStore = useUserStore();


let warningMessage = ref<string>()

if (mainStore.license) {
  let m = moment(String(mainStore.license.expiresOn));
  let dif = m.diff(moment(), 'days')
  if (dif <= 7) {
    warningMessage.value = `Your license expires in ${dif} days`
  }
}


const hasAdminAccess = computed(() => {

  return userStore.hasAdminAccess;
});


const authAvailable = computed(() => {
  return mainStore.authAvailable;
});

const userId = computed(() => {
  const userProfile = userStore.userProfile;
  if (userProfile) {
    return userProfile.user_id;
  } else {
    return "Guest";
  }
});

async function logout() {
  await userStore.userLogout();
}
</script>

<style scoped>


.background {
  background-image: url("~@/assets/wallpaper-skyline-reduced.jpg");
  background-position: 0 20%;
  background-size: auto 100%;
}

div.caption {
  font-size: 11px !important;
  align-self: center;
  text-align: center;
}

.v-list-item {
  padding: 0 10px;
}
</style>
<style>
header.v-toolbar a {
  text-decoration: none;
}
</style>
