<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
      dark
      color="deep-purple darken-3"
    >
      <!-- <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-btn @click.stop="miniVariant = !miniVariant" icon>
        <v-icon>mdi-{{ `chevron-${miniVariant ? 'right' : 'left'}` }}</v-icon>
      </v-btn>
      <v-btn @click.stop="clipped = !clipped" icon>
        <v-icon>mdi-application</v-icon>
      </v-btn>
      <v-btn @click.stop="fixed = !fixed" icon>
        <v-icon>mdi-minus</v-icon>
      </v-btn> -->
      <v-toolbar-title v-text="title" />
      <v-spacer />
      <!-- <v-btn @click.native="locateMe" icon>
        <v-icon>mdi-crosshairs-gps</v-icon>
      </v-btn> -->
      <v-btn @click.stop="rightDrawer = !rightDrawer" icon>
        <v-icon>mdi-menu</v-icon>
      </v-btn>
    </v-app-bar>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <v-navigation-drawer v-model="rightDrawer" :right="right" temporary fixed>
      <v-list>
        <v-list-item to="/" router exact>
          <v-list-item-action>
            <v-icon>mdi-home</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Home</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item to="locations" router exact>
          <v-list-item-action>
            <v-icon>mdi-map-marker</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Locations</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-snackbar
      v-model="snackbar"
      multi-line
      :color="errorStr ? 'red darken-1' : 'teal darken-2'"
    >
      <span v-if="errorStr">
        {{ errorStr }}
      </span>
      <span v-else-if="location">
        {{
          `Your location data is ${location.coords.latitude}, ${location.coords.longitude}`
        }}
      </span>
    </v-snackbar>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      location: null,
      snackbar: false,
      gettingLocation: false,
      errorStr: null,
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Welcome',
          to: '/'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Inspire',
          to: '/inspire'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'TEC'
    }
  },
  methods: {
    async getLocation() {
      await new Promise((resolve, reject) => {
        if (!('geolocation' in navigator)) {
          reject(new Error('Geolocation service is not available.'))
        }

        navigator.geolocation.getCurrentPosition(
          (success) => {
            console.log('success', success)
          },
          (error) => {
            console.log('error', error)
          }
        )
      })
    },
    async locateMe() {
      this.gettingLocation = true
      if (!('geolocation' in navigator)) {
        this.snackbar = true
        this.errorStr = 'Geolocation service is not available.'
      } else {
        await navigator.geolocation.getCurrentPosition(
          (success) => {
            this.snackbar = true
            this.location = success
            console.log('success', success)
          },
          (error) => {
            console.log('error', error)
          }
        )
      }
    }
  }
}
</script>
