<template>
    <div class="page" :class="{ noBlur: noBlur }">
        <div class="popup" v-if="complete">
            <div class="slide popup-background"></div>
            <div class="popup-content slide">
                <h1 class="grow">
                    <em>{{
                        currentRow === 1
                            ? 'Insane!'
                            : currentRow === 2
                            ? 'Smashed it.'
                            : currentRow === 3
                            ? 'Nice!'
                            : currentRow === 4
                            ? 'Decent Enough.'
                            : currentRow === 5
                            ? 'Just about!'
                            : `C'mon now...`
                    }}</em>
                </h1>
                <p class="popup-info">
                    You guessed the word {{ word.toUpperCase() }} in
                    {{ currentRow }} guess{{ currentRow > 1 ? 'es' : '' }}.
                </p>
                <button class="button" @click="resetGame()">Play Again</button>
            </div>
        </div>
        <div class="help">
            <p>
                Use the ← or → arrows to switch between blocks.<br />Submit your
                guess with Enter.
            </p>
        </div>
        <h3 class="blur">BLURDLE</h3>
        <div class="wordle-container">
            <Transition name="fade" mode="out-in">
                <WordleRow
                    v-if="currentRow === 0"
                    :key="currentRow + 0"
                    :word="word"
                    :row="0"
                    :currentRow="currentRow"
                />

                <WordleRowComplete
                    v-else
                    :key="currentRow + 420"
                    :word="word"
                    :row="0"
                    :currentRow="currentRow"
                    :completeWord="wordAttempts[0]"
                    :correctLetters="correctLetters"
                />
            </Transition>
            <Transition name="fade">
                <WordleRow
                    v-if="currentRow === 1"
                    :key="currentRow + 1"
                    :word="word"
                    :row="1"
                    :currentRow="currentRow"
                />

                <WordleRow
                    v-else-if="currentRow < 1"
                    :key="currentRow + 69"
                    :currentRow="currentRow"
                />

                <WordleRowComplete
                    v-else
                    :key="currentRow + 420"
                    :word="word"
                    :row="1"
                    :currentRow="currentRow"
                    :completeWord="wordAttempts[1]"
                    :correctLetters="correctLetters"
                />
            </Transition>

            <WordleRow
                v-if="currentRow === 2"
                :key="currentRow + 2"
                :word="word"
                :row="2"
                :currentRow="currentRow"
            />
            <WordleRow
                v-else-if="currentRow < 2"
                :key="currentRow + 69"
                :currentRow="currentRow"
            />

            <WordleRowComplete
                v-else
                :key="currentRow + 420"
                :word="word"
                :row="2"
                :currentRow="currentRow"
                :completeWord="wordAttempts[2]"
                :correctLetters="correctLetters"
            />

            <WordleRow
                v-if="currentRow === 3"
                :key="currentRow + 3"
                :word="word"
                :row="3"
                :currentRow="currentRow"
                :completeWord="wordAttempts[3]"
            />

            <WordleRow
                v-else-if="currentRow < 3"
                :key="currentRow + 69"
                :currentRow="currentRow"
            />

            <WordleRowComplete
                v-else
                :key="currentRow + 420"
                :word="word"
                :row="3"
                :currentRow="currentRow"
                :completeWord="wordAttempts[3]"
                :correctLetters="correctLetters"
            />

            <WordleRow
                v-if="currentRow === 4"
                :key="currentRow + 4"
                :word="word"
                :row="4"
                :currentRow="currentRow"
                :completeWord="wordAttempts[4]"
            />
            <WordleRow
                v-else-if="currentRow < 4"
                :key="currentRow + 69"
                :currentRow="currentRow"
            />

            <WordleRowComplete
                v-else
                :key="currentRow + 420"
                :word="word"
                :row="4"
                :currentRow="currentRow"
                :completeWord="wordAttempts[4]"
                :correctLetters="correctLetters"
            />

            <WordleRow
                v-if="currentRow === 5"
                :key="currentRow + 5"
                :word="word"
                :row="5"
                :currentRow="currentRow"
                :completeWord="wordAttempts[5]"
            />

            <WordleRow
                v-else-if="currentRow < 5"
                :key="currentRow + 69"
                :currentRow="currentRow"
            />

            <WordleRowComplete
                v-else
                :key="currentRow + 420"
                :word="word"
                :row="5"
                :currentRow="currentRow"
                :completeWord="wordAttempts[5]"
                :correctLetters="correctLetters"
            />
        </div>
        <div class="remove-blur-bar">
            <label class="switch">
                <input type="checkbox" v-model="noBlur" />
                <span class="slider round"></span>
            </label>
        </div>
    </div>
</template>

<script>
import Words from '../assets/words.json'
import WordleRow from './WordleRow.vue'
import WordleRowComplete from './WordleRowComplete.vue'

export default {
    name: 'PageTemplate',
    props: {
        msg: String,
    },
    components: {
        WordleRow,
        WordleRowComplete,
    },
    methods: {
        resetGame: function () {
            location.reload()
        },
    },
    data: function () {
        return {
            word: Words.words[Math.floor(Math.random() * Words.words.length)],
            currentRow: 0,
            wordAttempts: {},
            correctLetters: [],
            complete: false,
            noBlur: false,
        }
    },
    mounted: function () {
        console.log('Component mounted.')
        console.log(Words.words)
        console.log(this.word)
        console.log(this.currentRow)
        let self = this
        this.emitter.on('nextRow', function (response) {
            let word = response[0]
            let correctLetters = response[1]
            self.wordAttempts[self.currentRow] = word
            self.correctLetters = self.correctLetters.concat(correctLetters)
            self.currentRow += 1
        })
        this.emitter.on('finish', function () {
            self.complete = true
        })
    },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
@import url('https://fonts.googleapis.com/css2?family=Hubballi&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Raleway:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;1,100;1,200;1,300;1,400;1,500&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;1,100;1,200;1,300;1,400;1,500&display=swap');
h3 {
    margin: 40px 0 0 0;
    font-size: 80px;
    position: absolute;
    top: 0;
    width: 100vw;
    color: #ffffff;
    filter: blur(3px);
    transition: filter 0.3s ease-in-out, text-shadow 0.3s ease-in-out;
    text-shadow: #ffffff 0 0 10px;
}

h3:hover {
    filter: blur(0px);
    text-shadow: #ffffff 0 0 0px;
}
ul {
    list-style-type: none;
    padding: 0;
}
li {
    display: inline-block;
    margin: 0 10px;
}
a {
    color: #42b983;
}

.page {
    overflow: hidden;
}

* {
    font-family: 'Hubballi';
    font-weight: 300;
    padding: 0;
    margin: 0;
}

.wordle-container {
    padding: 150px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    overflow: hidden;
    z-index: 9;
}

.popup {
    height: 100vh;
    width: 100vw;
    background-color: #00000088;
    position: absolute;
    z-index: 99999999;
    display: flex;
    align-items: center;
    justify-content: center;
}

.popup-content {
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    align-content: center;
    padding: 50px 10px;
    width: 300px;
    margin: 0 0 0 -160px;
}

.popup-content * {
    padding: 10px;
    color: #ffffff;
}

.v-enter-active,
.v-leave-active {
    transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
    opacity: 0;
}

.help {
    position: absolute;
    bottom: 0;
    display: flex;
    flex-direction: row;
    align-content: center;
    justify-content: center;
    align-items: center;
    width: 100vw;
    overflow: hidden;
}

.help p {
    color: #ffffff;
    font-size: 24px;
    padding-bottom: 40px;
}

html,
body {
    padding: 0;
    margin: 0;
    overflow-x: hidden;
}

.button {
    background-color: #42b983;
    border: none;
    font-size: 22px;
    color: #ffffff;
    transition: transform 0.2s ease-out;
    margin: 20px 0 0 0;
    padding: 10px 15px;
    box-shadow: 2px 2px 8px #00000022;
}

.popup-info {
    font-size: 24px;
}

.button:hover {
    transform: scale(1.12);
}

.grow {
    animation: grow 2s forwards ease-in-out;
}

@keyframes grow {
    0% {
        transform: scale(1);
    }
    50% {
        transform: scale(2);
    }
    100% {
        transform: scale(1.5);
    }
}

@keyframes slide {
    0% {
        transform: translateY(-200%);
    }
    90% {
        transform: translateY(10%);
    }
    100% {
        transform: translateY(0);
    }
}

html {
    height: 100%;
    width: 100%;
    background: linear-gradient(
        16deg,
        rgb(11, 121, 165) 0%,
        rgb(204, 72, 123) 100%
    );
}

.noBlur * {
    filter: blur(0) !important;
}
.popup-background {
    position: relative;
    background-color: #ffffff77;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    align-content: center;
    padding: 50px 10px;
    margin: 0 -160px 0 0;
    width: 300px;
    height: 300px;
    border-radius: 30px;
    filter: blur(40px) !important;
}

.remove-blur-bar {
    position: absolute;
    right: 0;
    bottom: 0;
    margin: 15px;
}

/* The switch - the box around the slider */
.switch {
    position: relative;
    display: inline-block;
    width: 60px;
    height: 34px;
    transform: scale(0.7);
}

/* Hide default HTML checkbox */
.switch input {
    opacity: 0;
    width: 0;
    height: 0;
}

/* The slider */
.slider {
    position: absolute;
    cursor: pointer;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: #ccc;
    -webkit-transition: 0.4s;
    transition: 0.4s;
}

.slider:before {
    position: absolute;
    content: '';
    height: 26px;
    width: 26px;
    left: 4px;
    bottom: 4px;
    background-color: white;
    -webkit-transition: 0.4s;
    transition: 0.4s;
}

input:checked + .slider {
    background-color: #2196f3;
}

input:focus + .slider {
    box-shadow: 0 0 1px #2196f3;
}

input:checked + .slider:before {
    -webkit-transform: translateX(26px);
    -ms-transform: translateX(26px);
    transform: translateX(26px);
}

/* Rounded sliders */
.slider.round {
    border-radius: 34px;
}

.slider.round:before {
    border-radius: 50%;
}
</style>
