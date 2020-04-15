<template>
  <div>
    <b-card-group deck>
      <b-card :title="amount.label"  v-for="amount in amounts" :key="amount.type">
        <b-card-text>{{ amount.description }}</b-card-text>
        <table>
          <tr>
            <td>
              <img :src="getPictureUrlByResource('holz')" />
              <strong>:</strong>
            </td>
            <td>
              <b-input v-on:change="onCalculateDiff()" v-model="amount.holz" style="margin-left: 10%"></b-input>
            </td>
          </tr>
          <tr>
            <td>
              <img :src="getPictureUrlByResource('lehm')" />
              <strong>:</strong>
            </td>
            <td>
              <b-input v-on:change="onCalculateDiff()" v-model="amount.lehm" style="margin-left: 10%"></b-input>
            </td>
          </tr>
          <tr>
            <td>
              <img :src="getPictureUrlByResource('eisen')" />
              <strong>:</strong>
            </td>
            <td>
              <b-input v-on:change="onCalculateDiff()" v-model="amount.eisen" style="margin-left: 10%"></b-input>
            </td>
          </tr>
        </table>
      </b-card>
    </b-card-group>

    <hr />

<b-card bg-variant="dark" text-variant="white" title="Überproduktion bis zur letzten Ressource:">
  <b-card-text>
        <table class="resultTable" style="width:100%" >
          <tr v-for="resource in result.overproduction" :key="resource.type">
            <td style="font-size:40px; ">{{ capitalizeFLetter(resource.type) }}:</td>
            <td style="font-size:40px; text-align:right">{{ resource.amount }}</td>
            <td style="font-size:40px; color:red; padding-left:5%"><strong  v-if="resource.amount == 0">VERURSACHT ENGPASS</strong></td>
          </tr>
        </table>
  </b-card-text>
  <b-button href="#" variant="primary">Reset</b-button>
</b-card>

  </div>
</template>
<style scoped>
.resultTable {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 50%;
}


</style>
<script>
//import HelloWorld from './components/HelloWorld';

export default {
  name: "App",

  components: {
    // HelloWorld,
  },
  methods: {
     capitalizeFLetter: function(resource) { 
       return resource[0].toUpperCase() + resource.slice(1); 
    }, 
    
    onCalculateDiff: function() {
      localStorage.amounts = JSON.stringify(this.amounts)
      this.result.timeToCompletion.forEach(resource => {
        resource.timeNeeded = this.calculateTimeFor(resource.type)
      });
      
      const sortesResources = this.result.timeToCompletion.sort((a, b) => b.timeNeeded - a.timeNeeded)
      const maxDurationResource = sortesResources[0];

      this.result.overproduction.forEach(resource => {
        resource.amount = this.calculateOverproductionFor(resource.type, maxDurationResource)
      });
    },

    calculateTimeFor(resource) {
      const amountNeeded = this.amounts.filter(amount => amount.type == 'amountNeeded')[0][resource]
      const amoundHaving = this.amounts.filter(amount => amount.type == 'amountHaving')[0][resource]
      const amountProducing = this.amounts.filter(amount => amount.type == 'amountProducing')[0][resource]
      if (amountProducing == 0 || amountProducing == null) return;
      return  ( amountNeeded - amoundHaving ) / amountProducing
    },

    calculateOverproductionFor(givenResource, maxProductionResource) {
      const durationOfResource = this.result.timeToCompletion.filter(resource => resource.type == givenResource)[0].timeNeeded
      const amountProducing = this.amounts.filter(amount => amount.type == 'amountProducing')[0][givenResource]
      if (durationOfResource < 0 || amountProducing == null  || amountProducing == "") return;
      return Math.round((maxProductionResource.timeNeeded - durationOfResource) * amountProducing)
    },

    getPictureUrlByResource: function(resource){
      switch (resource) {
        case 'holz':
          return 'https://help.die-staemme.de/images/3/31/Holz.png';
        case 'lehm':
          return 'https://help.die-staemme.de/images/a/ae/Lehm.png';
        case 'eisen':
          return 'https://help.die-staemme.de/images/a/a5/Eisen.png';      
        default:
          break;
      }
    }
  },
  data: () => ({
    amounts: null,
    amount_template: [
      {
      type: 'amountHaving',
      label: 'Bestehende Mengen',
      description: 'Angabe bestehender Mengen',
      holz: null,
      lehm: null,
      eisen: null
    },
    {
      type: 'amountProducing',
      label: 'Stündliche Produktion',
      description: 'Wieviel Ressourcen werden pro Stunde produziert?',
      holz: null,
      lehm: null,
      eisen: null
    },
    {
      type: 'amountNeeded',
      label: 'Ressourcen benötigt',
      description: 'Wieviel Ressourcen werden insgesamt für den Bau benötigt? ',
      holz: null,
      lehm: null,
      eisen: null
    }
    ],
    result: {
      timeToCompletion: [
        {
          "type": "holz",
          "timeNeeded": null
        },
        {
          "type": "lehm",
          "timeNeeded": null
        },
        {
          "type": "eisen",
          "timeNeeded": null
        }
      ],
      overproduction: [
        {
          "type": "holz",
          "amount": null
        },
        {
          "type": "lehm",
          "amount": null
        },
        {
          "type": "eisen",
          "amount": null
        }
      ],
    }

  }),
    mounted() {
    if (localStorage.amounts) {
      this.amounts = JSON.parse(localStorage.amounts)
    } else {
      this.amounts = this.amount_template
    } 
  },
};
</script>
