<script setup>
import { SITE } from "~/site-info.js";
import albumsJSON from "~/public/data/albums.json";
import tracksJSON from "~/public/data/tracks.json";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { faClock } from "@fortawesome/free-regular-svg-icons";
definePageMeta({ layout: "site" });
</script>


<template>
  <main>
    <section id="album">
      <div class="container py-5" itemscope itemtype="http://schema.org/MusicAlbum">
        <div class="pb-3 text-center">
          <h1 class="mb-1"><span itemprop="name">{{ album.title }}</span> ({{ album.type }})</h1>
          <h3 class="text-secondary mb-0" itemprop="byArtist" itemscope itemtype="http://schema.org/MusicGroup"><span itemprop="name">{{ album.artists }}</span></h3>
        </div>
        <div class="text-center">
          <img :src="`/images/${album.cover}.jpg`" class="rounded-lg" itemprop="image" width="300" height="300">
        </div>
        <div class="row">
          <div class="col-12 info mt-3">
            <h3 class="mb-1">Tracks</h3>
            <table class="table text-secondary shadow-sm">
              <thead class="text-white">
                <tr>
                  <th>#</th>
                  <th>Artists</th>
                  <th>Title</th>
                  <th><FontAwesomeIcon :icon="faClock"/></th>
                </tr>
              </thead>
              <tbody>
                <template v-for="(track, index) in album.tracks" :key="track">
                  <tr @click="goTrack(track)" role="button" :itemprop="track" itemscope itemtype="http://www.schema.org/MusicRecording">
                    <td itemprop="position">{{ index + 1 }}</td>
                    <td itemprop="url" :content="`${SITE.url}/music/${track}`">{{ tracks[track].artists }}</td>
                    <td itemprop="name">{{ tracks[track].title }}</td>
                    <td itemprop="duration" :content="`PT${tracks[track]?.hh ? tracks[track].hh : 0}H${tracks[track]?.mm ? tracks[track].mm : 0}M${tracks[track]?.ss ? tracks[track].ss : 0}S`">{{ tracks[track].mm }}:{{ String(tracks[track].ss).padStart(2, "0") }}</td>
                  </tr>
                </template>
              </tbody>
            </table>
          </div>
          <div class="col-12 col-md-8 mt-3 text-secondary">
            <h3 class="mb-0 text-white">Description</h3>
            <p class="mb-md-0">{{ album.description }}</p>
          </div>
          <div class="col-12 col-md-4 mt-md-3">
            <div class="tags">
              <div class="mb-0">Type</div>
              <div class="tag mb-1">{{ album.type }}</div>
              <div class="mb-0">Release date</div>
              <div class="tag mb-1" itemprop="datePublished" :content="album.date.split('T')[0]">{{ new Date(album.date).toLocaleDateString("en-US", { year: "numeric", month: "short", day: "numeric" }) }}</div>
              <div class="mb-0">Fanlink</div>
              <div class="tag"><a :href="`https://yizack.com/${album.cover}`" target="_blank">yizack.com/{{ album.cover }}</a></div>
            </div>
          </div>
        </div>
        <div id="more-projects" class="pt-3">
          <h3 class="text-center">More <a class="tag" href="/album">Albums</a></h3>
          <div class="row gallery text-center">
            <template v-for="(album, param) in moreAlbums" :key="param">
              <div class="col-6 col-lg-3" >
                <div class="item">
                  <NuxtLink :to="`/album/${param}/`">
                    <img class="img-fluid scale-on-hover" :src="`/images/${album.cover}.jpg`" :alt="`${album.artists} - ${album.title} (${album.type})`">
                    <p class="mt-2 mb-0">{{ album.title }} ({{ album.type }})</p>
                    <p><small>{{ album.artists }}</small></p>
                  </NuxtLink>
                </div>
              </div>
            </template>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script>
export default {
  name: "AlbumPage",
  data() {
    return {
      param: this.$route.params.album,
      albums: albumsJSON,
      tracks: tracksJSON,
    };
  },
  methods: {
    goTrack(track) {
      this.$nuxt.$router.push(`/music/${track}`);
    },
  },
  computed: {
    album() {
      return this.albums[this.param] || {};
    },
    moreAlbums() {
      return Object.entries(this.albums).slice(0, 8).reduce((obj, [key, value]) => {
        obj[key] = value;
        return obj;
      }, {});
    }
  },
  created() {
    useHead({
      title: `${this.album.artists} - ${this.album.title} (${this.album.type})`,
      meta: [
        { name: "keywords", content: `album, ${this.album.title}, fanlink, download` },
        { name: "description", content: this.album.description },
        // Protocolo Open Graph
        { property: "og:url", content: `${SITE.url}/album/${this.param}/` },
        { property: "og:type", content: "website" },
        { property: "og:title", content: `${this.album.artists} - ${this.album.title} (${this.album.type})` },
        { property: "og:site_name", content: SITE.name },
        { property: "og:image", content: `${SITE.url}/images/${this.album.cover}.jpg` },
        { property: "og:image:width", content: "500" },
        { property: "og:image:height", content: "500" },
        { property: "og:image:alt", content: `${this.album.artists} - ${this.album.title} (${this.album.type})` },
        { property: "og:description", content: this.album.description },
        // Twitter Card
        { name: "twitter:card", content: "summary" },
        { name: "twitter:image", content: `${SITE.url}/images/${this.album.cover}.jpg` },
        { name: "twitter:title", content: `${this.album.artists} - ${this.album.title} (${this.album.type})` },
        { name: "twitter:description", content: this.album.description },
        { name: "twitter:site", content: `@${SITE.twitter}` }
      ],
      link: [
        { rel: "canonical", href: `${SITE.url}/album/${this.param}/` }
      ]
    });
  }
};
</script>