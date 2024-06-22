<script setup>
import { ref } from "vue";
import { countries } from "../countries.js";

const fromLang = ref(null);
const toLang = ref(null);
const translate_button = ref(null);
const speak_button = ref(null);

let inputText = ref(null);
let outputText = ref(null);

const fromSelect = ref(false);
const toSelect = ref(false);

const getFromCountry = (value, key) => {
  fromLang.value.innerHTML = value;
  fromLang.value.setAttribute("value", key);
  fromSelect.value = false;
};

const getToCountry = (value, key) => {
  toLang.value.innerHTML = value;
  toLang.value.setAttribute("value", key);
  toSelect.value = false;
};

const translateText = () => {
  console.log(fromLang.value.innerHTML.replace(/\s+/g, ''));
  if (
    fromLang.value.innerHTML.replace(/\s+/g, '') != "Translatefrom" &&
    toLang.value.innerHTML.replace(/\s+/g, '') != "Translateto" &&
    inputText.value
  ) {
    const apiUrl = `https://api.mymemory.translated.net/get?q=${encodeURIComponent(
      inputText.value
    )}&langpair=${fromLang.value.innerHTML}|${toLang.value.innerHTML}`;

    fetch(apiUrl)
      .then((response) => response.json())
      .then((data) => {
        if (data.responseData) {
          const translatedText = data.responseData.translatedText;
          const formattedText = removeQuestionMarks(translatedText);
          outputText.value.innerText = formattedText;
        } else {
          outputText.value.innerText = "Error : Could Not Translate!";
        }
      })
      .catch((error) => {
        console.error("Error:", error);
        outputText.value.innerText =
          "Error : An error occurred while translating!";
      });
  }
};

function removeQuestionMarks(text) {
  return text.replace(/^¿+|¿+$/g, "");
}

function speakText() {
  if (toLang.value.getAttribute("value")) {
    const speechSynthesis = window.speechSynthesis;
    const speechUtterance = new SpeechSynthesisUtterance(
      outputText.value.innerHTML
    );
    speechUtterance.lang = toLang.value.getAttribute("value");
    speechSynthesis.speak(speechUtterance);
  }
}
</script>
<template>
  <div class="text-center">
    <div
      class="flex flex-col align-middle items-center gap-3 md:flex-row md:gap-10 justify-center"
    >
      <div class="w-[300px]">
        <div class="ui-wrapper translate-from">
          <label
            ref="fromLang"
            @click="fromSelect = !fromSelect"
            class="dropdown-container"
            for="dropdown"
            >Translate from
          </label>
          <div
            class="select-wrapper transition-all duration-[2s]"
            :class="[fromSelect ? 'visible opacity-1' : 'hidden opacity-0']"
          >
            <ul>
              <li
                v-for="(Valuecoutry, keyCountry) in countries"
                :key="keyCountry"
                @click="getFromCountry(Valuecoutry, keyCountry)"
              >
                <label :for="Valuecoutry"
                  ><span class="">{{ Valuecoutry }}</span></label
                >
              </li>
            </ul>
          </div>
        </div>
        <div class="card translated-text" ref="translated-text">
          <div class="body">
            <textarea v-model="inputText" class="text" rows="10" />
          </div>
        </div>
      </div>

      <div class="w-[300px]">
        <div class="ui-wrapper translate-to">
          <label
            ref="toLang"
            class="dropdown-container"
            for="dropdown"
            @click="toSelect = !toSelect"
            >Translate to
          </label>
          <div
            class="select-wrapper"
            :class="[toSelect ? 'visible opacity-1' : 'hidden opacity-0']"
          >
            <ul>
              <li
                v-for="(Valuecoutry, keyCountry) in countries"
                :key="keyCountry"
                @click="getToCountry(Valuecoutry, keyCountry)"
              >
                <label :for="Valuecoutry"
                  ><span class="">{{ Valuecoutry }}</span></label
                >
              </li>
            </ul>
          </div>
        </div>
        <div class="card output-text" ref="output-text">
          <div class="body">
            <textarea ref="outputText" readonly class="text" rows="10" />
          </div>
        </div>
      </div>
    </div>
    <button
      ref="translate_button"
      @click="translateText"
      class="translate-button block mx-auto my-5 button"
    >
      <p class="text">Translate</p>
    </button>
    <button
      @click="speakText"
      ref="speak_button"
      class="speak-button block mx-auto button"
    >
      <p class="text">Speak</p>
    </button>
  </div>
</template>

<style>
::selection {
  background: #c0c3d7;
  color: #30344c;
}
::-moz-selection {
  background: #c0c3d7;
  color: #30344c;
}

.ui-wrapper {
  --width: 300px;
  --height: 50px;
  --background: #fff;
  --text-color: rgb(73, 73, 73);
  --border-color: rgb(185, 184, 184);
  --border-focus-color: rgb(0, 110, 255);
  --shadow-color: rgba(34, 60, 80, 0.2);
  --shadow-focus-color: rgba(0, 110, 255, 0.2);
  --dropdown-button-color: #e6e6e6;
  --dropdown-button-hover-color: #dad9d9;
}

.ui-wrapper *,
.ui-wrapper *::before,
.ui-wrapper *::after {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  font-family: sans-serif;
  color: var(--text-color);
}

.ui-wrapper {
  height: var(--height);
  display: -webkit-inline-box;
  display: -ms-inline-flexbox;
  display: inline-flex;
  border-radius: 10px;
  position: relative;
  border: 1px solid var(--border-color);
  background-color: var(--background);
  -webkit-box-pack: justify;
  -ms-flex-pack: justify;
  justify-content: space-between;
  -webkit-box-shadow: 0px 2px 5px 0px var(--shadow-color);
  box-shadow: 0px 2px 5px 0px var(--shadow-color);
  -webkit-transition: 0.4s;
  -o-transition: 0.4s;
  transition: 0.4s;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  margin-bottom: 10px;
  z-index: 1000;
}

.ui-wrapper > input {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  margin: 0;
  width: 0;
  height: 0;
  opacity: 0;
  position: absolute;
  left: 9999px;
}

.dropdown-container {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
  width: 300px;
  padding: 0 10px;
  cursor: pointer;
  border-radius: 9px;
  background-color: var(--dropdown-button-color);
}

.dropdown-container::after {
  content: "";
  background-image: url("data:image/svg+xml;base64,PHN2ZyBmaWxsPSIjNDk0OTQ5IiB3aWR0aD0iNzAwcHQiIGhlaWdodD0iNzAwcHQiIHZlcnNpb249IjEuMSIgdmlld0JveD0iMCAwIDcwMCA3MDAiCiAgICB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIj4KICAgIDxwYXRoCiAgICAgICAgZD0ibTM4MC4zOSA0ODQuNzkgMzA3LjA0LTMwNS45OWMxNi43NjYtMTcuODEyIDE2Ljc2Ni00NS4wNTkgMC02MS44MjgtMTYuNzY2LTE2Ljc2Ni00NS4wNTktMTYuNzY2LTYxLjgyOCAwbC0yNzUuNiAyNzUuNi0yNzUuNi0yNzUuNmMtMTcuODEyLTE2Ljc2Ni00NS4wNTktMTYuNzY2LTYxLjgyOCAwLTE2Ljc2NiAxNi43NjYtMTYuNzY2IDQ0LjAxMiAwIDYxLjgyOGwzMDUuOTkgMzA1Ljk5YzE3LjgxMiAxNi43NjYgNDUuMDU5IDE2Ljc2NiA2MS44MjggMHoiCiAgICAgICAgZmlsbC1ydWxlPSJjdXJyZW50Q29sb3IiIC8+Cjwvc3ZnPg==");
  width: 12px;
  height: 12px;
  background-position: center;
  background-size: cover;
  right: 10px;
  position: absolute;
  -webkit-transition: 0.2s;
  -o-transition: 0.2s;
  transition: 0.2s;
}

.select-wrapper {
  width: var(--width);
  position: absolute;
  top: 60px;
  left: 0;
  -webkit-transition: 0.2s;
  -o-transition: 0.2s;
  transition: 0.2s;
}

.select-wrapper ul {
  position: absolute;
  width: 100%;
  background-color: white;
  border-radius: 10px;
  padding: 10px;
  margin: 0;
  left: 0;
  width: 298px;
  list-style: none;
  height: 285px;
  display: -webkit-inline-box;
  display: -ms-inline-flexbox;
  display: inline-flex;
  -webkit-box-orient: vertical;
  -webkit-box-direction: normal;
  -ms-flex-direction: column;
  flex-direction: column;
  -webkit-box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
  overflow-y: auto;
  white-space: nowrap;
}

/* width */
::-webkit-scrollbar {
  width: 0px;
}

.select-wrapper ul li {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
  padding: 5px;
  border-radius: 5px;
}

.select-wrapper ul li label {
  width: 100%;
}

.select-wrapper ul li,
.select-wrapper ul li * {
  cursor: pointer;
}

.select-wrapper ul li:hover {
  background: lightgray;
}

.select-wrapper ul li span {
  display: inline-block;
  margin-right: 15px;
}

.input-wrapper {
  width: 100%;
  padding-left: 10px;
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-orient: vertical;
  -webkit-box-direction: normal;
  -ms-flex-direction: column;
  flex-direction: column;
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  justify-content: center;
  row-gap: 2px;
}

.ui-wrapper:focus-within {
  border-color: var(--border-focus-color);
  -webkit-box-shadow: 0px 2px 5px 0px var(--shadow-focus-color);
  box-shadow: 0px 2px 5px 0px var(--shadow-focus-color);
}

.dropdown-container:hover {
  background-color: var(--dropdown-button-hover-color);
}

.card {
  position: relative;
  background-color: #30344c;
  padding: 1em;
  z-index: 5;
  box-shadow: 4px 4px 20px rgba(0, 0, 0, 0.3);
  border-radius: 10px;
  max-width: 300px;
  transition: 200ms ease-in-out;
}

.username {
  color: #c6e1ed;
  font-size: 0.85em;
  font-weight: 600;
  position: absolute;
  bottom: 10px;
}

.body {
  display: flex;
  flex-direction: column;
}

.body .text {
  margin: 0 10px 0 0;
  white-space: pre-line;
  background: #30344c;
  border: none;
  outline: none;
  color: #c0c3d7;
  font-weight: 400;
  line-height: 1.5;
  padding: 5px;
  margin-bottom: 4px;
}

.body .text::-webkit-resizer {
  display: none;
  visibility: none;
}

.viewer span {
  height: 20px;
  width: 20px;
  background-color: rgb(28, 117, 219);
  margin-right: -6px;
  border-radius: 50%;
  border: 1px solid #fff;
  display: grid;
  align-items: center;
  text-align: center;
  font-weight: bold;
  font-size: 8px;
  color: #fff;
  padding: 2px;
}

.viewer span svg {
  stroke: #fff;
}

.button {
  padding: 10px 15px;
  background-color: #30344c;
  outline: 3px #30344c solid;
  outline-offset: -3px;
  border-radius: 5px;
  border: none;
  cursor: pointer;
  transition: 400ms;
  width: 150px;
  box-shadow: 1px 1px  10px ;
}

.button .text {
  color: white;
  font-weight: 700;
  font-size: 1em;
  transition: 400ms;
}


.button:hover {
  background-color: transparent;
  opacity: 0.6;
}

.button:hover svg path:nth-child(2) {
  fill: #30344c;
}
</style>
