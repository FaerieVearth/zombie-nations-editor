<template>
  <div class="waves-section">
    <div class="level-display">
      <h1>Level: {{ levelIndex + 1 }}</h1>
      <h4>Enemies: {{ getNumberOfZombies() }}</h4>
    </div>
    <div class="draggable-container">
      <draggable
        v-model="tableItems"
        v-bind="dragOptions"
        :move="checkMove"
        @start="drag = true"
        @end="drag = false"
      >
        <div v-for="(element, index) in tableItems" :key="element.id">
          <div class="wave-row-container">
            <div class="wave-row" @mousedown="setHandleToggle(false)">
              <round-slider
                :value="element[0]"
                @input="(value) => inputHandler(value, index, 'angle')"
                start-angle="90"
                end-angle="450"
                animation="false"
                max="360"
                pathColor="#AAA"
                rangeColor="#6e2eb6"
                tooltipColor="#000"
                width="8"
                radius="30"
                step="15"
                editableTooltip="false"
              />
              <b-form-select
                class="size-max"
                :value="element[1]"
                @input="(value) => inputHandler(value, index, 'unit')"
                :options="[
                  0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15,
                ]"
              ></b-form-select>
              <div class="image-container">
                <img
                  class="zombie-image"
                  :src="require('../assets/' + element[1] + '.png')"
                  :alt="element[1]"
                />
              </div>
              <b-form-input
                :formatter="formatNumber"
                class="size-max"
                type="number"
                :value="element[2]"
                @change="
                  (value) => inputHandler(value, index, 'number_of_units')
                "
              ></b-form-input>
              <div :key="index" class="size-max">
                <b-button
                  class="delete-button"
                  variant="danger"
                  @click="removeRowHandler(index)"
                  >Remove</b-button
                >
              </div>
            </div>
            <div
              class="move-handle"
              @mousedown="setHandleToggle(true)"
              @mouseup="setHandleToggle(false)"
            >
              <svg
                width="2rem"
                height="2rem"
                viewBox="0 0 24 24"
                fill="none"
                xmlns="http://www.w3.org/2000/svg"
                aria-hidden="true"
                focusable="false"
              >
                <path
                  d="M16.563 5.587a2.503 2.503 0 1 0 0-5.007 2.503 2.503 0 0 0 0 5.007Z"
                  fill="#212134"
                ></path>
                <path
                  d="M18.487 3.083c-.012.788-.487 1.513-1.229 1.797a1.954 1.954 0 0 1-2.184-.574A1.943 1.943 0 0 1 14.9 2.11c.4-.684 1.2-1.066 1.981-.927a1.954 1.954 0 0 1 1.606 1.9c.011.748 1.17.748 1.158 0A3.138 3.138 0 0 0 17.565.17c-1.176-.423-2.567-.03-3.36.933-.83 1.002-.968 2.45-.284 3.575.678 1.124 1.993 1.674 3.273 1.431 1.432-.272 2.428-1.593 2.451-3.019.012-.753-1.147-.753-1.158-.006ZM16.563 14.372a2.503 2.503 0 1 0 0-5.007 2.503 2.503 0 0 0 0 5.007Z"
                  fill="#212134"
                ></path>
                <path
                  d="M18.487 11.867c-.012.789-.487 1.513-1.229 1.797a1.954 1.954 0 0 1-2.184-.574 1.943 1.943 0 0 1-.174-2.196c.4-.684 1.2-1.066 1.981-.927.928.156 1.588.968 1.606 1.9.011.748 1.17.748 1.158 0a3.138 3.138 0 0 0-2.08-2.914c-1.176-.423-2.567-.029-3.36.933-.83 1.002-.968 2.45-.284 3.575.678 1.124 1.993 1.675 3.273 1.431 1.432-.272 2.428-1.593 2.451-3.019.012-.753-1.147-.753-1.158-.005ZM16.563 23.392a2.503 2.503 0 1 0 0-5.006 2.503 2.503 0 0 0 0 5.006Z"
                  fill="#212134"
                ></path>
                <path
                  d="M18.487 20.89c-.012.787-.487 1.512-1.229 1.796a1.954 1.954 0 0 1-2.184-.574 1.943 1.943 0 0 1-.174-2.196c.4-.684 1.2-1.066 1.981-.927.928.156 1.588.967 1.606 1.9.011.748 1.17.748 1.158 0a3.138 3.138 0 0 0-2.08-2.914c-1.176-.423-2.567-.03-3.36.933-.83 1.002-.968 2.45-.284 3.575.678 1.124 1.993 1.674 3.273 1.431 1.432-.272 2.428-1.593 2.451-3.019.012-.753-1.147-.753-1.158-.006ZM7.378 5.622a2.503 2.503 0 1 0 0-5.007 2.503 2.503 0 0 0 0 5.007Z"
                  fill="#212134"
                ></path>
                <path
                  d="M9.302 3.119c-.011.788-.486 1.512-1.228 1.796a1.954 1.954 0 0 1-2.185-.574 1.943 1.943 0 0 1-.173-2.196c.4-.684 1.199-1.066 1.981-.927a1.943 1.943 0 0 1 1.605 1.9c.012.748 1.17.748 1.16 0A3.138 3.138 0 0 0 8.38.205c-1.176-.423-2.567-.029-3.36.933-.83 1.002-.968 2.45-.285 3.575.678 1.124 1.994 1.675 3.274 1.431 1.431-.272 2.428-1.593 2.451-3.019.012-.753-1.147-.753-1.159-.005ZM7.378 14.406a2.503 2.503 0 1 0 0-5.006 2.503 2.503 0 0 0 0 5.006Z"
                  fill="#212134"
                ></path>
                <path
                  d="M9.302 11.902c-.011.788-.486 1.513-1.228 1.797a1.954 1.954 0 0 1-2.185-.574 1.943 1.943 0 0 1-.173-2.196c.4-.684 1.199-1.066 1.981-.927a1.943 1.943 0 0 1 1.605 1.9c.012.748 1.17.748 1.16 0A3.138 3.138 0 0 0 8.38 8.988c-1.176-.423-2.567-.03-3.36.933-.83 1.002-.968 2.45-.285 3.575.678 1.124 1.994 1.674 3.274 1.431 1.431-.272 2.428-1.593 2.451-3.019.012-.753-1.147-.753-1.159-.006ZM7.378 23.427a2.503 2.503 0 1 0 0-5.007 2.503 2.503 0 0 0 0 5.007Z"
                  fill="#212134"
                ></path>
                <path
                  d="M9.302 20.924c-.011.788-.486 1.513-1.228 1.797a1.954 1.954 0 0 1-2.185-.574 1.943 1.943 0 0 1-.173-2.196c.4-.684 1.199-1.066 1.981-.927.933.156 1.594.967 1.605 1.9.012.748 1.17.748 1.16 0A3.139 3.139 0 0 0 8.38 18.01c-1.176-.423-2.567-.03-3.36.933-.83 1.002-.968 2.45-.285 3.569.678 1.124 1.994 1.675 3.274 1.431 1.431-.272 2.428-1.593 2.451-3.019.012-.747-1.147-.747-1.159 0Z"
                  fill="#212134"
                ></path>
              </svg>
            </div>
          </div>
        </div>
      </draggable>
    </div>
    <div class="action-container">
      <b-button class="add-button" variant="success" @click="addRowHandler"
        >Add Wave</b-button
      >
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";
export default {
  name: "EditableTable",
  components: {
    draggable,
  },
  props: {
    value: Array,
    fields: Array,
    levelIndex: Number,
  },
  data() {
    return {
      tableItems: this.value,
      handleToggle: false
    };
  },
  methods: {
    getNumberOfZombies() {
      let zombieCount = 0;
      for (let i = 0; i < this.tableItems.length; i++) {
        zombieCount += this.tableItems[i][2];
      }
      return zombieCount;
    },
    setHandleToggle(x) {
      this.handleToggle = x;
    },
    checkMove() {
      return this.handleToggle;
    },
    formatNumber(number) {
      if (
        number < 0 ||
        number === undefined ||
        number === null ||
        number === ""
      ) {
        return 0;
      }
      return number;
    },
    inputHandler(value, index, key) {
      if (key === "unit") {
        this.tableItems[index][1] = parseInt(value);
      } else if (key === "angle") {
        this.tableItems[index][0] = parseInt(value);
      } else if (key === "number_of_units") {
        this.tableItems[index][2] = parseInt(value);
      }
      this.$set(this.tableItems, index, this.tableItems[index]);
      this.$emit("input", this.tableItems);
    },
    addRowHandler() {
      const newRow = [15, 0, 1];
      this.tableItems.push(newRow);
      this.$emit("input", this.tableItems);
    },
    removeRowHandler(index) {
      this.tableItems = this.tableItems.filter((item, i) => i !== index);
      this.$emit("input", this.tableItems);
    },
  },
  computed: {
    dragOptions() {
      return {
        animation: 200,
        disabled: false,
      };
    },
  },
};
</script>

<style>
.image-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-width: 60px;
}
.zombie-image {
  height: 50px;
}
.draggable-container::-webkit-scrollbar-track {
  border-radius: 10px;
  background: #b0c2b0;
}

.draggable-container::-webkit-scrollbar {
  border-radius: 10px;
  width: 16px;
  background: #b0c2b0;
}

.draggable-container::-webkit-scrollbar-thumb {
  border-radius: 10px;
  background-color: #657165;
}
.waves-section {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  width: 600px;
  min-width: 600px;
  background-image: linear-gradient(#b0c2b0, #869b86);
  padding: 1rem;
  margin: 1rem;
  border-radius: 15px;
}
.level-display {
  display: flex;
  gap: 3rem;
  width: 100%;
  justify-content: space-between;
  align-items: end;
}
.action-container {
  margin-bottom: 10px;
}
.action-container button {
  margin-right: 5px;
}
.delete-button {
  margin-left: 5px;
}
.size-max {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 50px;
}
.draggable-container {
  display: flex;
  padding-right: 10px;
  overflow-x: hidden;
  overflow-y: auto;
}
.wave-row-container {
  display: flex;
  justify-content: center;
  background: none;
  margin: 5px 0px 5px 0px;
}
.wave-row {
  display: flex;
  gap: 2em;
  padding: 0.6rem;
  justify-content: center;
  align-items: center;
  background: rgb(242, 241, 241);
  border-bottom-left-radius: 15px;
  border-top-left-radius: 15px;
}

.move-handle {
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  background: rgb(205, 205, 205);
  border-bottom-right-radius: 15px;
  border-top-right-radius: 15px;
}
</style>
