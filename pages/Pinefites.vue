<template>
  <div>
    <v-text-field
      append-icon='search'
      single-line
      hide-details
      v-model='search'
      autofocus
      @keyup.enter='mapRequest'
    ></v-text-field>
    <div id='map'></div>
    <v-container grid-list-md>
      <v-layout>
        <google-map
          :markers='markers'
          :showWindow='showWindow'>
        </google-map>
        <place-list
          :places='places'
          @selectWindow='selectWindow'
          @add='add'
          @remove='remove'>
        </place-list>
      </v-layout>
    </v-container>
  </div>
</template>

<script>
  import { mapActions } from 'vuex';
  import GoogleMap from '../components/GoogleMap';
  import PlaceList from '../components/PlaceList';

  export default {
    components: { GoogleMap, PlaceList },
    data() {
      return {
        search: '',
        showWindow: null,
        markers: [{
          position: {
            lat: 49.2780937,
            lng: -122.91988329999998
          },
          infoText: 'Simon Fraser University',
          id: 'ChIJJyJ6hcB5hlQRW7Uwbj9sUAA'
        }],
        places: [{
          name: 'Simon Fraser University',
          rating: 5,
          id: 'ChIJJyJ6hcB5hlQRW7Uwbj9sUAA'
        }]
      }
    },
    methods: {
      ...mapActions([
        'addMarkerPlaceSelect',
        'removeMarkerPlaceSelect'
      ]),
      add(id) {
        const payload = {};
        for (let i = 0; i < this.markers.length; i++) {
          if (this.markers[i].id === id) {
            payload.marker = this.markers[i];
            break;
          }
        }
        for (let j = 0; j < this.places.length; j++) {
          if (this.places[j].id === id) {
            payload.place = this.places[j];
            break;
          }
        }
        this.addMarkerPlaceSelect(payload);
      },
      remove(id) {
        this.removeMarkerPlaceSelect(id);
      },
      mapRequest() {
        this.showWindow = null;
        const zenefits = new google.maps.LatLng(49.2780937, -122.91988329999998);
        const request = {
          location: zenefits,
          radius: '1000',
          query: this.search
        };
        const service = new google.maps.places.PlacesService(document.getElementById('map'));
        service.textSearch(request, (res, status) => {
          this.places = res.map(ele => {
            return {
              name: ele.name,
              rating: ele.rating,
              id: ele.id
            };
          });
          this.markers = res.map(ele => {
            return {
              position: {
                lat: ele.geometry.location.lat(),
                lng: ele.geometry.location.lng()
              },
              infoText: ele.name,
              id: ele.id
            };
          });
        });
      },
      selectWindow(id) {
        this.markers.forEach(ele => {
          if (ele.id === id)
            this.showWindow = ele;
        });
      }
    }
  };
</script>
