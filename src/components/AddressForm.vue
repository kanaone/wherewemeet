<template>
    <div class="container">
      <div class="title">
        <h1>Where We Meet</h1>
      </div>
			<div class="flex-container">
        <div class="address-inputs">
        <div v-for="(address, index) in addresses" :key="index" class="address-input">
          <input type="text" :id="'address-input-' + index" v-model="addresses[index]" placeholder="Entrez une adresse">
         <!-- <button @click="removeAddress(index)">-</button>--> <!-- Bouton de suppression -->
        </div>
        <button @click="addAddress">+</button>
      </div>
      <div id="map" class="map"></div>
    </div></div>
  </template>
  
  <script>
  import L from 'leaflet';
  import 'leaflet/dist/leaflet.css';
  
  export default {
    data() {
      return {
        addresses: ['', ''],
        map: null,
        userLocation: [43.6045, 1.4442], // Coordonnées par défaut (Paris)
        isGoogleApiLoaded: false
      };
    },
    methods: {

      removeAddress(index) {
        if (this.addresses.length > 1) {
          this.addresses.splice(index, 1);
        }
      },
      addAddress() {
        this.addresses.push('');
        this.$nextTick(() => {
          this.initializeAutocomplete(this.addresses.length - 1);
        });
      },
      initializeAutocomplete(index) {
        if (typeof google === 'undefined' || typeof google.maps === 'undefined') {
        console.error("Google Maps API n'est pas chargé");
        return;
        }
        const input = document.getElementById('address-input-' + index);
        const autocomplete = new google.maps.places.Autocomplete(input);
        autocomplete.addListener('place_changed', () => {
          const place = autocomplete.getPlace();
          this.addresses[index] = place.formatted_address;
        });
      },
      initializeMap() {
        this.map = L.map('map').setView(this.userLocation, 13);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
          attribution: '© OpenStreetMap contributors'
        }).addTo(this.map);
      },
      locateUser() {
        this.initializeMap();
        // Ici, ajoutez la logique pour localiser l'utilisateur, si nécessaire
      }
    },
    mounted() {
      this.addresses.forEach((_, index) => {
        this.initializeAutocomplete(index);
      });
      this.locateUser();
    }
  };

  </script>
  
  <style>
  .container {
    text-align: center;
  }
  
  .title {
    margin-bottom: 20px;
    font-family: "Pacifico", sans-serif;
  }
  


	.address-inputs {
    margin-bottom: 10px; /* Espace entre les lignes d'adresse */
	}

	.address-inputs input {
    margin-right: 10px; /* Espace entre l'input et le bouton */
	}

	.address-inputs button {
		margin-right: 20px; /* Espace entre les champs d'adresse et la carte */
	}

	.map {
		height: 400px; /* Hauteur spécifique pour la carte */
		width: 600px; /* Largeur spécifique pour la carte */
		/* Autres styles pour la carte */
	}
  </style>
  