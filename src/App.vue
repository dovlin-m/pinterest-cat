<template>
  <v-app>
    <v-app id="inspire">
      <v-card>
        <v-toolbar
            color="#2196F3"
            dark
            dense
            height="64px"
            class="shadow"
        >
          <v-tabs
              v-model="tab"
              align-with-title
          >
            <v-tab active-class="active">
              Все котики
            </v-tab>
            <v-tab class="pa-27" active-class="active">
              Любимые котики
            </v-tab>
          </v-tabs>
        </v-toolbar>

        <v-tabs-items v-model="tab">
          <v-tab-item>
            <v-item-group
                multiple
                class="v-item-group-img"
            >
              <v-card
                  class="d-flex justify-space-between flex-wrap"
                  flat
                  tile
              >
                <v-flex
                    v-for="n in images" :§="n.id"
                    column
                >
                  <v-item class="hover-img">
                    <v-img
                        :src="n.url"
                        height="225"
                        width="225"
                        lazy-src="https://picsum.photos/10/6"
                        class="text-right pa-4 ma-6"
                    >
                      <button
                          @mouseover="hover=true"
                          @mouseleave="hover=false"
                          v-on:click="getFavourites(n)"
                          icon
                          dark
                      >
                        <v-icon
                            v-if="hover"
                            x-large
                        >
                          mdi-heart
                        </v-icon>
                        <v-icon
                            v-else
                            x-large
                        >
                          {{ getActive(n.id) ? 'mdi-heart' : 'mdi-heart-outline' }}
                        </v-icon>
                      </button>
                    </v-img>
                  </v-item>
                </v-flex>
              </v-card>
            </v-item-group>
            <div>
              <p class="loading-cats">... загружаем еще котиков ...</p>
            </div>
          </v-tab-item>

          <v-tab-item>
            <v-item-group
                multiple
                class="v-item-group-img"
            >
              <v-card
                  class="d-flex justify-space-between flex-wrap"
                  flat
                  tile
              >
                <v-flex
                    v-for="n in favourites" :§="n.id"
                    column
                >
                  <v-item class="hover-img">
                    <v-img
                        :src="n.url"
                        height="225"
                        width="225"
                        lazy-src="https://picsum.photos/10/6"
                        class="text-right pa-4 ma-6"
                    >
                      <button
                          @mouseover="hover=true"
                          @mouseleave="hover=false"
                          v-on:click="getFavourites(n)"
                          icon
                          dark
                      >
                        <v-icon
                            v-if="hover"
                            x-large>
                          mdi-heart-outline
                        </v-icon>
                        <v-icon
                            v-else
                            x-large
                        >
                          mdi-heart
                        </v-icon>
                      </button>
                    </v-img>
                  </v-item>
                </v-flex>
              </v-card>
            </v-item-group>
          </v-tab-item>
        </v-tabs-items>
      </v-card>
    </v-app>
  </v-app>
</template>

<script>
import { axios } from "@/plugins/axios";

export default {
  name: 'App',

  data: () => ({
    hover: false,
    tab: null,
    initialData: [],
    images: [],
    favourites:[],
    error_message:null,
    limit: 15,
  }),

  created() {
    for (let i = 0; i < localStorage.length; i++) {
      const keyImg = localStorage.key(i)
      const dateImg = JSON.parse(localStorage.getItem(`${keyImg}`))
      this.favourites.push(dateImg)
    }

    axios.defaults.headers.common['x-api-key'] = "DEMO-API-KEY"

    this.getImages();
  },

  methods:{
    async getImages()
    {
      try{

        let query_params = {
          limit: this.limit
        }
        let response = await axios.get('https://api.thecatapi.com/v1/images/search', { params: query_params } )
        this.initialData = response.data
        for (const img of this.initialData) {
          this.images.push(img)
        }

      } catch(err){
        console.log(err)
      }
    },
    async getFavourites(n) {
      if (localStorage.getItem(`${n.id}`)) {
        localStorage.removeItem(`${n.id}`)
        const newArr = []
        for (let i = 0; i < localStorage.length; i++) {
          const keyImg = localStorage.key(i)
          const dateImg = JSON.parse(localStorage.getItem(`${keyImg}`))
          newArr.push(dateImg)
        }
        this.favourites = newArr
      } else {
        for (const img of this.images) {
          if (img.id === n.id) {
            localStorage.setItem(`${n.id}`, JSON.stringify(img))
            const dateImg = JSON.parse(localStorage.getItem(`${n.id}`))
            this.favourites.push(dateImg)
          }
        }
      }
    },

    getActive(key) {
      if (localStorage.getItem(key)) {
        return true
      } else {
        return false
      }
    },

    getNextImages() {
      window.onscroll = () => {
        let bottomOfWindow = document.documentElement.scrollTop + window.innerHeight === document.documentElement.offsetHeight;
        if (bottomOfWindow) {
          this.getImages()
        }
      }
    }
  },

  mounted() {
    this.getNextImages();
  }
};
</script>

<style>
.loading-cats {
  font-size: 14px;

  text-align: center;

  letter-spacing: 0.25px;
  color: #000000;
}

.v-card > :first-child:not(.v-btn):not(.v-chip):not(.v-avatar),
.v-card > .v-card__progress + :not(.v-btn):not(.v-chip):not(.v-avatar) {
  border-top-left-radius: unset;
  border-top-right-radius: unset;
}

.hover-img:hover {
  transform: scale(1.1378);
  box-shadow: 0px 6px 5px rgba(0, 0, 0, 0.24), 0px 9px 18px rgba(0, 0, 0, 0.18);
 }

.hover-img:hover .v-responsive__content {
  display: block;
}

.v-responsive__content {
  display: none;
  padding-top: 147px;
}

.theme--light.v-icon {
  color: #FF3A00;
}

.v-item-group-img {
  padding: 24px 38px;
}

.pa-27 {
  padding: 0 27px;
}

.v-card {
  border: none;
}

.shadow {
  z-index: 1;
  box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.24);
}

.v-application--is-ltr .v-tabs--align-with-title >
.v-tabs-bar:not(.v-tabs-bar--show-arrows):not(.v-slide-group--is-overflowing) >
.v-slide-group__wrapper > .v-tabs-bar__content >
.v-tab:first-child, .v-application--is-ltr .v-tabs--align-with-title >
.v-tabs-bar:not(.v-tabs-bar--show-arrows):not(.v-slide-group--is-overflowing) >
.v-slide-group__wrapper > .v-tabs-bar__content > .v-tabs-slider-wrapper + .v-tab {
  margin-left: 62px;
}

.v-tab {
  min-width: 120px;
  font-weight: 400;
  letter-spacing: 0.25px;
  text-transform: none;
}

.v-tabs-slider-wrapper {
  display: none;
}

.v-sheet.v-card:not(.v-sheet--outlined) {
  box-shadow: none;
}

.active {
  background-color: #1E88E5;
}
</style>

