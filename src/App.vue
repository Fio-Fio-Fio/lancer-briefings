<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Event Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "003",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "000",
          "name": "Prelude",
          "status": "success"
        },
        {
         "slug": "001",
         "name": "Unregulated Scrap",
         "status": "success"
       },
       {
         "slug": "002",
         "name": "Vigilant Gaze",
         "status": "success"
       },
       {
         "slug": "003",
         "name": "Floodgate",
         "status": "success"
       },
      ],
      "pilots": [
        {
          "callsign": "Chariot",
          "alias": "Jóhanna Landon",
          "code": "//Landon.Jóhanna:b722b21d-cc41-4792-9d7a-a36d6b5a45d8//NDL-C-OMEGA-CRYSTAL",
          "corpro": "HA",
          "frame": "SALADIN",
          "mech": "Armor Clad Faith"
        },
        {
          "callsign": "Carolina",
          "alias": "Dr. Lyra Vickers Jr.",
          "code": "//Vickers.Lyra:f6188940-66f2-4630-9baf-d04296bd0847//NDL-C-BLACK-ROSE",
          "corpro": "SSC",
          "frame": "Swallowtail",
          "mech": "ἑλένας ἕλανδρος ἑλέπτολις"
        },
        {
          "callsign": "Strideshaker",
          "alias": '"Coral"',
          "code": "//Richelieu.Coralie:2770c0df-4ef4-47d6-afab-a77eccc7a71f//NDL-C-DELTA-STELE",
          "corpro": "SSC",
          "frame": "MONARCH",
          "mech": "WORLDBREAKER"
        },
        {
          "callsign": "Baron",
          "alias": "Trine of the 12th Chalice",
          "code": "//12th.Chalice.Trine:0260f49a-7d11-4858-94e5-7f897855a53f//NDL-C-FIRST-DECEMBER",
          "corpro": "GMS",
          "frame": "Sagarmatha",
          "mech": "Drive Heaven From Night"
        },
        {
          "callsign": "Vixie",
          "alias": "Vinessa",
          "code": "//Vinessa:0fe170f0-5054-4af3-a9c4-df922b5db73a//NDL-C-FALLEN-DREAM",
          "corpro": "SSC",
          "frame": "Swallowtail",
          "mech": "Smoke and Ash"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u/499ay",
        "system": "Ardennes-3",
        "gate": "Atlas-Ouanoukrim",
        "ring": "Atlas-Line",
        "headerTitle": "DoJ/HR",
        "headerSubtitle": "Union Auxillary Unit A42",
        "subheaderTitle": "Peacekeepers",
        "subheaderSubtitle": "Mechanized Cavalry",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
