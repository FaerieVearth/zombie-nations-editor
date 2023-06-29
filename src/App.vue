<template>
  <div id="app">
    <div class="waves-editor-container">
      <div class="level-selection">
        <div class="import-export">
          <b-button @click="importFile()" variant="success">Import</b-button>
          <b-button @click="exportFile()" variant="success">Export</b-button>
        </div>
        <div class="level-button-container">
          <div
            v-for="(value, levelName, index) in wavesPlan"
            v-bind:key="index"
          >
            <div class="button-container">
              <b-button
                v-bind:class="[
                  'level-button',
                  { 'selected-level': levelName === selectedLevel },
                ]"
                @click="setSelectedLevelHandler(levelName)"
              >
                Level: {{ getLevelName(levelName) }}
              </b-button>
              <div
                v-bind:class="[
                  'hide-dropdown',
                  { 'show-dropdown': levelName === selectedLevel },
                ]"
              >
                <b-dropdown
                  offset="-130"
                  class="dropdown-icon"
                  split-class="split-styling"
                >
                  <b-dropdown-item @click="duplicateLevel(levelName)"
                    ><div class="duplicate-level">Duplicate +</div>
                  </b-dropdown-item>
                  <b-dropdown-item @click="deleteLevel(levelName)"
                    ><div class="delete-level">Delete!</div>
                  </b-dropdown-item>
                </b-dropdown>
              </div>
            </div>
          </div>
        </div>
      </div>
      <template v-for="(value, levelName, index) in wavesPlan">
        <EditableTable
          v-if="levelName === selectedLevel"
          v-bind:key="index + levelName"
          v-model="wavesPlan[levelName]"
          :fields="fields"
          :levelIndex="levelName"
        ></EditableTable>
      </template>
      <WavePlayer :waves="wavesPlan[selectedLevel]"></WavePlayer>
    </div>
  </div>
</template>

<script>
import EditableTable from "./components/EditableTable.vue";
import WavePlayer from "./components/WavePlayer.vue";
import wavesPlanJson from "../data/wavesPlan.json";
export default {
  name: "App",
  components: {
    EditableTable,
    WavePlayer,
  },
  data() {
    return {
      selectedLevel: 0,
      fields: [
        { key: "angle", label: "Angle", type: "angle" },
        {
          key: "unit",
          label: "Unit",
          type: "select",
          options: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15],
        },
        { key: "number_of_units", label: "Number of Units", type: "number" },
        { key: "remove", label: "", type: "remove" },
      ],
      wavesPlan: {},
      gameDataInMemory: {},
    };
  },
  mounted: function () {
    this.$nextTick(function () {
      this.wavesPlan = wavesPlanJson.waves_v2;
      this.gameDataInMemory = wavesPlanJson;
    });
  },
  methods: {
    getLevelName(level) {
      return parseInt(level) + 1;
    },
    setSelectedLevelHandler(wave) {
      this.selectedLevel = wave;
      this.$forceUpdate();
    },
    deleteLevel(wave) {
      this.wavesPlan.splice(wave, 1);
      this.selectedLevel = 0;
    },
    getNumberOfZombies() {
      let zombieCount = 0;
      for (let i = 0; i < this.tableItems.length; i++) {
        zombieCount += this.tableItems[i][2];
      }
      return zombieCount;
    },
    duplicateLevel(wave) {
      this.wavesPlan.splice(wave, 0, this.wavesPlan[wave]);
      this.selectedLevel = wave+1;
    },
    formatLevels() {
      let newKey = 0;
      const newArray = [];
      for (let key = 0; key <= this.wavesPlan.length; key++) {
        newArray[newKey] = this.wavesPlan[key];
        newKey++;
      }
      console.log(newArray);
      this.wavesPlan = newArray;
    },
    async importFile() {
      let fileHandle;
      [fileHandle] = await window.showOpenFilePicker();
      const file = await fileHandle.getFile();
      const content = await file.text();
      this.gameDataInMemory = JSON.parse(content);
      this.wavesPlan = JSON.parse(content).waves_v2;
    },
    async exportFile() {
      const handle = await window.showSaveFilePicker();
      const writable = await handle.createWritable();
      this.gameDataInMemory.waves_v2 = this.wavesPlan;
      await writable.write(JSON.stringify(this.gameDataInMemory));
      await writable.close();
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Comfortaa:wght@300;400;500&display=swap");

#app {
  font-family: "Comfortaa";
  background-image: linear-gradient(
    to right,
    #3e4936,
    #707c6b 25%,
    #3e4936 50%,
    #707c6b 75%,
    #3e4936 100%
  );
}
.waves-editor-container {
  display: flex;
  height: 100vh;
  width: 100vw;
}

.level-selection {
  display: flex;
  flex-direction: column;
  padding: 2em;
  background-image: linear-gradient(#b0c2b0, #869b86);
  padding: 1rem;
  margin: 1rem;
  border-radius: 15px;
}

.level-button-container {
  padding-right: 10px;
  overflow-x: hidden;
  overflow-y: auto;
}

.level-button-container::-webkit-scrollbar-track {
  border-radius: 10px;
  background: #b0c2b0;
}

.level-button-container::-webkit-scrollbar {
  border-radius: 10px;
  width: 16px;
  background: #b0c2b0;
}

.level-button-container::-webkit-scrollbar-thumb {
  border-radius: 10px;
  background-color: #657165;
}

.level-button {
  width: 180px;
  margin: 0.2em 0 0.2em 0;
}
.button-container {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: nowrap;
}
.selected-level {
  margin-left: 1em;
  background: #bb2d3b !important;
  border-top-right-radius: 0 !important;
  border-bottom-right-radius: 0 !important;
}

.dropdown-icon button {
  background: rgb(196, 194, 194);
  border: rgb(201, 198, 198) 1px solid;
  border-top-left-radius: 0 !important;
  border-bottom-left-radius: 0 !important;
}

.hide-dropdown {
  display: none;
}

.show-dropdown {
  display: block;
}

.delete-level {
  color: #bb2d3b;
  margin: 1em 0 1em 0;
}

.duplicate-level {
  color: green;
  margin: 1em 0 1em 0;
}

.import-export {
  display: flex;
  flex-direction: column;
  gap: 0.2em;
  padding: 1rem;
}
</style>
