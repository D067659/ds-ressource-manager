<template>
  <div>
      <b-navbar variant="dark" type="dark">
    <b-navbar-brand>
      Die Stämme Ressourcen Manager
    </b-navbar-brand>
  </b-navbar>
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
              <b-input-group>
                <b-input v-on:change="onCalculateDiff()" v-model="amount.holz" style="margin-left: 10%"></b-input>
                <b-button v-on:click="amount.holz *= 1000; onCalculateDiff()" variant="outline-success"><strong>K</strong></b-button>
              </b-input-group>
            </td>
          </tr>
          <tr>
            <td>
              <img :src="getPictureUrlByResource('lehm')" />
              <strong>:</strong>
            </td>
            <td>
              <b-input-group>
                <b-input v-on:change="onCalculateDiff()" v-model="amount.lehm" style="margin-left: 10%"></b-input>
                <b-button v-on:click="amount.lehm *= 1000; onCalculateDiff()" variant="outline-success"><strong>K</strong></b-button>
              </b-input-group>
            </td>
          </tr>
          <tr>
            <td>
              <img :src="getPictureUrlByResource('eisen')" />
              <strong>:</strong>
            </td>
            <td>
              <b-input-group>
                <b-input v-on:change="onCalculateDiff()" v-model="amount.eisen" style="margin-left: 10%"></b-input>
                <b-button v-on:click="amount.eisen *= 1000; onCalculateDiff()" variant="outline-success"><strong>K</strong></b-button>
              </b-input-group>
            </td>
          </tr>
        </table>
        <div v-if="amount.type == 'amountNeeded'" style="margin-top:2%">
          <b-button v-on:click="amount.holz='40000'; amount.lehm='50000'; amount.eisen='50000'; onCalculateDiff()" variant="outline-primary" style="margin-left: 5%; margin-right: 3%"><b-icon icon="award"></b-icon> Adelsgeschlecht</b-button>
          <b-button v-on:click="amount.holz='28000'; amount.lehm='30000'; amount.eisen='25000'; onCalculateDiff()" variant="outline-primary"><b-icon icon="life-preserver"></b-icon> Goldmünze</b-button>        
        </div>
      </b-card>
    </b-card-group>

    <hr />
    <b-card-group>
<b-card bg-variant="dark" text-variant="white" title="Dauer bis zu den notwendigen Rohstoffen:">
  <b-card-text>
        <table class="resultTable" style="width: 100%">
          <tr v-for="resource in result.timeToCompletion" :key="resource.type">
            <td style="font-size:40px; ">{{ capitalizeFLetter(resource.type) }}:</td>
            <td style="font-size:40px; text-align:right">{{ decimalToHoursAndMins(resource.timeNeeded) }}</td>
          </tr>
        </table>
  </b-card-text>
</b-card>

<img src="https://dsde.innogamescdn.com/asset/cc7606f/graphic/start2/bg-paladin.png" style="margin-left: 2%; margin-right:2%">

<b-card bg-variant="dark" text-variant="white" title="Überproduktion bis zur letzten Ressource (in hh:mm)):">
  <b-card-text>
        <table class="resultTable" style="width: 100%">
          <tr v-for="resource in result.overproduction" :key="resource.type">
            <td style="font-size:40px; ">{{ capitalizeFLetter(resource.type) }}:</td>
            <td style="font-size:40px; text-align:right">{{ resource.amount }}</td>
            <td style="font-size:20px; color:red; padding-left:5%; width: 100%"><strong  v-if="resource.amount == 0">VERURSACHT ENGPASS</strong></td>
          </tr>
        </table>
  </b-card-text>
</b-card>
    </b-card-group>


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

export default {
  name: "App",
  methods: {
     capitalizeFLetter: function(resource) { 
       return resource[0].toUpperCase() + resource.slice(1); 
    }, 

    decimalToHoursAndMins: function(hours) {
      var sign = hours < 0 ? "-" : "";
      var hour = Math.floor(Math.abs(hours));
      var min = Math.floor((Math.abs(hours) * 60) % 60);
      return sign + (hour < 10 ? "0" : "") + hour + ":" + (min < 10 ? "0" : "") + min;
    },

    onCalculateDiff: function() {
      localStorage.amounts = JSON.stringify(this.amounts)
      this.result.timeToCompletion.forEach(resource => {
        resource.timeNeeded = this.calculateTimeFor(resource.type)
      });

      const cloneTimeCompletion = [...this.result.timeToCompletion];
      const sortesResources = cloneTimeCompletion.sort((a, b) => b.timeNeeded - a.timeNeeded)
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
      this.onCalculateDiff()
    } else {
      this.amounts = this.amount_template
    } 
  },
};
</script>
