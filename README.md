# How-to-use-google-api-complete-in-Nuxt.js
The code below was development by me and was created for get informations from research by users on fields with autocomplete google maps.

# Dependencies
I used module nuxt-google-maps-module wich can be found in packages on npm
link to module: https://www.npmjs.com/package/nuxt-google-maps-module

# Note: you need add module in nuxt.config.js

# How to use
I created this solution to return city's name choised by users, but you can edit this solution according need.

```
<template>
  <div>
    <input 
      id="location" 
      type="text" 
      placeholder="Input with your city"
      v-model:"city" 
      v-on:keyup.enter="onKeyUpEnterCompleteSearch()" />
  </div>
</template

<script>
export default {
  name: "Home",
  data() {
    return {
      city: null,
    }
  },
  //called in lifecycle updated, because sometimes happen not load module in lifecycle mounted
  updated() {
    this.autoComplete();
  },
  methods: {
    //load AutoComplete
    autoComplete() {
      if (this.autocomplete != null || this.$google == null) return;

      //LOAD API GOOGLE AND USE
      this.autocomplete = new this.$google.maps.places.Autocomplete(
        document.getElementById("location"),
        {
          types: ['(cities)'],
          componentRestrictions: { country: "br" }
        }
      );

      // EVENT LISTENER AUTO COMPLETE
      this.$google.maps.event.addListener( this.autocomplete, "place_changed", () => {
          let place = this.autocomplete.getPlace(); //get place choosed when clicked
          let ac = place.address_components; //return details of place 
          
          if (ac == undefined) return;

          //loop for find informations specific
          for (let i = 0; i < ac.length; i++) {
            if (ac[i].types.includes("administrative_area_level_2")) {
              this.city = ac[i].long_name;  //set variable and input to name of city, example sorocaba 
              return;
            }
          }
        }
      );
    },

    //Event Listener key up enter
    onKeyUpEnterCompleteSearch(){
      let options = {
        input: this.city,
        componentRestrictions: {
          country: 'br'
        },
        types: ['(cities)']
      };
      
      //created a service to search same results from autocomplete when pressioned enter key.
      let complete = new this.$google.maps.places.AutocompleteService(); 
      
      //created a geocoder to search details more specific of places.
      let geocoder = new this.$google.maps.Geocoder();

      complete.getPlacePredictions(options, (place, status) => {
        if(status == this.$google.maps.places.PlacesServiceStatus.OK){
          //show all results from auto complete
          // console.log(place[0]);

          geocoder.geocode({placeId: place[0].place_id}, (placeDetails, stat) => {
            if(stat == this.$google.maps.GeocoderStatus.OK){
              //show detail first result from auto complete
              // console.log(placeDetails);

              let arr = placeDetails[0].address_components;
              for (let i = 0; i < arr.length; i++) {
                if (arr[i].types.includes("administrative_area_level_2")) {
                  this.city = arr[i].long_name; //set variable and input to name of city, example sorocaba 
                  return;
                }
              }   
            }
          })
        }    
      });
    },
  
  }

}

</script>

<style lang="scss">
</style>

```
