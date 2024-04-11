<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions">
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
    <section class="section-container" id="events">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="min-width: 600px;">
      <div style="height: 52px; overflow: hidden">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute"
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
  <Footer />
</template>

<script>
import Header from "./components/layout/Header.vue";
import Footer from "./components/layout/Footer.vue";
import Mission from "./components/Mission.vue";
import Pilot from "./components/Pilot.vue";
import Markdown from "vue3-markdown-it";

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown,
  },

  data() {
    return {
      mission_slug: "001",
      current_md: "",
      events: "",
      missions: [
        {
          slug: "001",
          name: "Danish Gambit",
          status: "start",
        },
      ],
      pilots: [
        {
          callsign: "H.A.C-K",
          alias: "Robert",
          code: "731b67c5-8484-42aa-8d27-ce0b29dac7bb//NDL-C-SOLAR-CLOSE",
          corpro: "HORUS",
          frame: "Goblin",
          mech: "G-0-0-BL-1-N",
        },
        {
          callsign: "RED SKY",
          alias: "Frederick Valasky",
          code: "e25d6fcf-75b0-4504-add5-443b792668a7//NDL-C-DELTA-CRYSTAL",
          corpro: "HARRISON ARMORY",
          frame: "GENGHIS",
          mech: "HELLHOUND",
        },
        {
          callsign: "CELLULAR",
          alias: "Newt Beamwave",
          code: "61493929-34f4-4d69-b1db-d8d3445580f7//NDL-C-RAW-ORCHID",
          corpro: "SSC",
          frame: "BLACK WITCH",
          mech: "BLACK WITCH",
        },
        {
          callsign: "GOLDEN LOTUS",
          alias: "Markon",
          code: "7c577ba6-eb4a-4d7a-bf31-f8ecef408820//NDL-C-SIGMA-SKULL",
          corpro: "SSC",
          frame: "STANDING HERE I REALISE",
          mech: "ATLAS",
        },
      ],
      header: {
        planet: "Ganymede Mining Colony",
        year: "5014u",
        system: "LL-34-G",
        gate: "Atlas-Quanokrim",
        ring: "Atlas-Line",
        headerTitle: "Full Metal Jacket",
        headerSubtitle: "Mining Company",
        subheaderTitle: "Liberation Front",
        subheaderSubtitle: "Alpha-Zero-Zero-One",
      },
      options: {
        eventsMarkdownPerMission: true,
      },
    };
  },

  created() {
    this.loadMissionMarkdown();
    this.loadEventsMarkdown();
  },

  computed: {},

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown();
      if (this.options.eventsMarkdownPerMission) {
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`;
      var client = new XMLHttpRequest();
      client.open("GET", md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      };
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if (self.options.eventsMarkdownPerMission) {
        md = `/events/${self.mission_slug}.md`;
      } else {
        md = "/events.md";
      }

      var client = new XMLHttpRequest();
      client.open("GET", md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      };
      client.send();
    },
  },
};
</script>

<style lang="scss">
#app {
  overflow: hidden;
}
</style>
