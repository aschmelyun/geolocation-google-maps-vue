<template>
    <div>
        <div style="max-width: 800px; margin: 0 auto; display: flex; align-items: center; justify-content: space-between">
            <div>
                <h1>Your coordinates:</h1>
                <p>{{ myCoordinates.lat }} Latitude, {{ myCoordinates.lng }} Longitude</p>
            </div>
            <div>
                <h1>Map coordinates:</h1>
                <p>{{ mapCoordinates.lat }} Latitude, {{ mapCoordinates.lng }} Longitude</p>
            </div>
        </div>
        <GmapMap
            :center="myCoordinates"
            :zoom="zoom"
            style="width:640px; height:360px; margin: 32px auto;"
            ref="mapRef"
            @dragend="handleDrag"
        ></GmapMap>
    </div>
</template>
<script>
    export default {
        data() {
            return {
                map: null,
                myCoordinates: {
                    lat: 0,
                    lng: 0
                },
                zoom: 7
            }
        },
        created() {
            // does the user have a saved center? use it instead of the default
            if(localStorage.center) {
                this.myCoordinates = JSON.parse(localStorage.center);
            } else {
                // get user's coordinates from browser request
                this.$getLocation({})
                    .then(coordinates => {
                        this.myCoordinates = coordinates;
                    })
                    .catch(error => alert(error));
            }

            // does the user have a saved zoom? use it instead of the default
            if(localStorage.zoom) {
                this.zoom = parseInt(localStorage.zoom);
            }
        },
        mounted() {
            // add the map to a data object
            this.$refs.mapRef.$mapPromise.then(map => this.map = map);
        },
        methods: {
            handleDrag() {
                // get center and zoom level, store in localstorage
                let center = {
                    lat: this.map.getCenter().lat(),
                    lng: this.map.getCenter().lng()
                };
                let zoom = this.map.getZoom();

                localStorage.center = JSON.stringify(center);
                localStorage.zoom = zoom;
            }
        },
        computed: {
            mapCoordinates() {
                if(!this.map) {
                    return {
                        lat: 0,
                        lng: 0
                    };
                }

                return {
                    lat: this.map.getCenter().lat().toFixed(4),
                    lng: this.map.getCenter().lng().toFixed(4)
                }
            }
        }
    }
</script>