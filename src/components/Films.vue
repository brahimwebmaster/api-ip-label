<template>
  <div>
    <v-row>
      <v-col cols="12" sm="6" md="6">
        <v-text-field
          v-model="title"
          label="Fim search"
          @keyup="searchFilms()"
          placeholder="Search Film"
          outlined
        ></v-text-field>
      </v-col>
    </v-row>
    <v-row>
      <div
        v-if="totalResults"
        class="display-1"
        style="width: 100%; text-align: center;"
      >Total Results : {{ totalResults }}</div>
      <div
        v-if="totalResults==0 && title.length > 0"
        class="display-1"
        style="width: 100%; text-align: center;"
      >there are no films with this title</div>
    </v-row>
    <v-row>
      <v-simple-table width="10">
        <template v-slot:default>
          <thead>
            <tr>
              <th v-for="header in filmsHeaders" :key="header" class="text-left">{{ header }}</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="film in films"
              :key="film.imdbID"
              @click="getFilmDetails(film.imdbID)"
              style="cursor: pointer"
            >
              <td>{{ film.Title }}</td>
              <td>{{ film.Year }}</td>
              <td>{{ film.imdbID }}</td>
              <td>{{ film.Type }}</td>
              <td>{{ film.Poster }}</td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>
      <v-pagination
        v-if="films.length > 0"
        v-model="pageNumber"
        :length="totalPageNumber"
        circle
        :total-visible="10"
        @input="getCurrentPage()"
      ></v-pagination>
      <v-dialog v-model="dialog" width="1000px" v-if="filmDetails">
        <v-card>
          <v-card-title>
            <span class="headline center">Film Details</span>
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col cols="12" sm="6" md="6">
                <img :src="filmDetails.Poster" alt />
              </v-col>
              <v-col cols="12" sm="6" md="6">
                <span class="font-weight-black">Title :</span>
                {{ filmDetails.Title }}
                <br />
                <span class="font-weight-black">Year :</span>
                {{ filmDetails.Year }}
                <br />
                <span class="font-weight-black">Country :</span>
                {{ filmDetails.Country }}
                <br />
                <span class="font-weight-black">Released :</span>
                {{ filmDetails.Released }}
                <br />
                <span class="font-weight-black">Actors :</span>
                {{ filmDetails.Actors }}
                <br />
                <span class="font-weight-black">Awards :</span>
                {{ filmDetails.Awards }}
                <br />
                <span class="font-weight-black">Plot :</span>
                {{ filmDetails.Plot }}
              </v-col>
            </v-row>
          </v-card-text>
          <v-card-actions>
            <div class="flex-grow-1"></div>
            <v-btn color="green darken-1" text @click="dialog = false">Close</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-row>
  </div>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      title: "",
      dialog: false,
      filmDetails: null,
      films: [],
      filmsHeaders: [],
      pageNumber: 1,
      totalPageNumber: 1,
      totalResults: 0,
      apiPath: "http://www.omdbapi.com/?apikey=8f0a5987&"
    };
  },
  methods: {
    searchFilms: function() {
      if (this.title.length > 3) {
        this.$http
          .get(this.apiPath + "s=" + this.title + "&page=" + this.pageNumber)
          .then(function(data) {
            if (data.body.Response == "False") {
              this.totalResults = 0;
              this.films = [];
              this.filmsHeaders = [];
            } else {
              this.totalResults = data.body.totalResults;
              this.films = data.body.Search;
              this.totalPageNumber = Math.ceil(
                data.body.totalResults / data.body.Search.length
              );
              if (this.filmsHeaders.length == 0) {
                this.filmsHeaders = Object.keys(this.films[0]);
              }
            }
          });
      }
    },
    getFilmDetails(filmId) {
      this.$http
        .get(this.apiPath + "i=" + filmId + "&plot=full")
        .then(function(data) {
          this.filmDetails = data.body;
          this.dialog = true;
        });
    },
    getCurrentPage() {
      this.searchFilms();
    }
  }
};
</script>

<style>
body {
  margin: 0;
  font-family: "Nunito SemiBold";
}
</style>