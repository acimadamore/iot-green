 
<template>
  <div class="configuration">
    <form>
      <v-text-field
        v-model="name"
        label="Nombre"
        required
      ></v-text-field>

      <v-text-field
        v-model.number="minHumidity"
        label="Nivel minimo de humedad"
        required
      ></v-text-field>

      <v-text-field
        v-model.number="maxHumidity"
        label="Nivel maximo de humedad"
        required
      ></v-text-field>

      <v-text-field
        v-model.number="watering"
        label="Tiempo de regado(ms)"
        required
      ></v-text-field>

      <v-btn @click="submit">Save</v-btn>
      <v-btn @click="cancel">Cancel</v-btn>
    </form>
  </div>
</template>

<script>

// @ is an alias to /src
import axios        from 'axios'

export default {
  name: 'configuration',
  props: ['id'],
  data: function() {
    return {
      uid: this.id,
      name: '',
      minHumidity: 0,
      maxHumidity: 0,
      watering: 0
    }
  },

  mounted() {
    axios
      .get(`http://raspberry-pi:4567/api/water-stations/${this.uid}`)
      .then((response) => {
        this.name         = response.data["name"]
        this.minHumidity  = response.data["low_soil_humidity"]
        this.maxHumidity  = response.data["high_soil_humidity"]
        this.watering     = response.data["watering_time"]
      })
  }, 

  methods: {
    cancel(){
      this.$router.push('/')
    },
    submit(){
      axios.post(`http://raspberry-pi:4567/api/water-stations/${this.uid}`, {
        _method:     'patch',
        name:        this.name,
        minHumidity: this.minHumidity,
        maxHumidity: this.maxHumidity,
        watering:    this.watering
      })
      .then(response => this.$router.push('/'))
    }
  }
}
</script>
