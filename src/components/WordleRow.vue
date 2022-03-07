<template>
    <div class="wordle-row-container" ref="container">
        <form @submit.prevent="wordSubmitted()">
            <WordleBlock
                ref="letter1"
                :key="0 + currentRow"
                :letter="letter1"
                :otherLetters="[
                    word ? removeLetterFromWord(letter1, word) : '',
                ]"
                :index="0"
                :row="row"
                :correctLetters="correctLetters"
                :currentRow="currentRow"
                :completeLetter="completeLetters[0]"
            />
            <WordleBlock
                ref="letter2"
                :key="1 + currentRow"
                :letter="letter2"
                :otherLetters="[
                    word ? removeLetterFromWord(letter2, word) : '',
                ]"
                :index="1"
                :row="row"
                :correctLetters="correctLetters"
                :currentRow="currentRow"
                :completeLetter="completeLetters[1]"
            />
            <WordleBlock
                ref="letter3"
                :key="2 + currentRow"
                :letter="letter3"
                :otherLetters="[
                    word ? removeLetterFromWord(letter3, word) : '',
                ]"
                :index="2"
                :row="row"
                :correctLetters="correctLetters"
                :currentRow="currentRow"
                :completeLetter="completeLetters[2]"
            />
            <WordleBlock
                ref="letter4"
                :key="3 + currentRow"
                :letter="letter4"
                :otherLetters="[
                    word ? removeLetterFromWord(letter4, word) : '',
                ]"
                :index="3"
                :row="row"
                :correctLetters="correctLetters"
                :currentRow="currentRow"
                :completeLetter="completeLetters[3]"
            />
            <WordleBlock
                ref="letter5"
                :key="4 + currentRow"
                :letter="letter5"
                :otherLetters="[
                    word ? removeLetterFromWord(letter5, word) : '',
                ]"
                :index="4"
                :row="row"
                :correctLetters="correctLetters"
                :currentRow="currentRow"
                :completeLetter="completeLetters[4]"
            />
            <input
                class="submit-button"
                v-if="row === currentRow"
                type="submit"
                ref="submit"
                value="Submit"
                @click="wordSubmitted()"
            />
        </form>
    </div>
</template>

<script>
import WordleBlock from '../components/WordleBlock.vue'
import Answers from '../assets/answers.json'
import Words from '../assets/words.json'

export default {
    name: 'WordleRow',
    components: { WordleBlock },
    props: {
        word: {
            type: String,
            required: false,
        },
        row: {
            type: Number,
            required: false,
        },
        currentRow: {
            type: Number,
            required: false,
        },
        completeWord: {
            type: String,
            required: false,
        },
    },
    data: function () {
        return {
            letter1: '',
            letter2: '',
            letter3: '',
            letter4: '',
            letter5: '',
            correctLetters: [],
            completeLetters: [],
        }
    },
    methods: {
        removeLetterFromWord: function (letter, word) {
            let wordArray = word.split('')
            return wordArray.filter((e) => e !== letter)
        },
        wordSubmitted: function () {
            this.emitter.emit('wordSubmitted')
        },
    },
    mounted: function () {
        console.log(typeof this.word)
        if (typeof this.word === 'undefined') {
            return
        }
        let emitter = this.emitter
        let refs = this.$refs
        let thisRow = this.row
        let thisWord = this.word
        let thisCorrectLetters = this.correctLetters

        this.letter1 = this.word.substring(0, 1)
        this.letter2 = this.word.substring(1, 2)
        this.letter3 = this.word.substring(2, 3)
        this.letter4 = this.word.substring(3, 4)
        this.letter5 = this.word.substring(4)
        // this.completeLetters = this.completeWord.split('')

        emitter.on('submitWord', function (row) {
            if (row !== thisRow) {
                return
            }
            let word = {}
            // let numberOfSubmissions = 0
            if (refs.letter1.letterInputLower !== '') {
                word.letter1 = refs.letter1.letterInputLower
            }
            if (refs.letter2.letterInputLower !== '') {
                word.letter2 = refs.letter2.letterInputLower
            }
            if (refs.letter3.letterInputLower !== '') {
                word.letter3 = refs.letter3.letterInputLower
            }
            if (refs.letter4.letterInputLower !== '') {
                word.letter4 = refs.letter4.letterInputLower
            }
            if (refs.letter5.letterInputLower !== '') {
                word.letter5 = refs.letter5.letterInputLower
            }


            console.log(Object.keys(word).length)
            if (Object.keys(word).length !== 5) {
                refs.container.classList.add('shake')
                console.log("WOOOOOOOOOOOOOOOO")
                console.log(word)

                return setTimeout(
                    () => refs.container.classList.remove('shake'),
                    820
                )
            }
            let wordString =
                (refs.letter1.letterInputLower +
                refs.letter2.letterInputLower +
                refs.letter3.letterInputLower +
                refs.letter4.letterInputLower +
                refs.letter5.letterInputLower)

                console.log(Words.words)
                console.log(Answers.words)
                console.log(wordString)
            
            if (!(Answers.words.includes(wordString) || Words.words.includes(wordString))) {
                refs.container.classList.add('shake')

                return setTimeout(
                    () => refs.container.classList.remove('shake'),
                    820
                )
            }

            Object.keys(word).forEach((letter) => {
                let lowercaseLetter = word[letter].toLowerCase()
                if (thisWord.includes(lowercaseLetter)) {
                    thisCorrectLetters.push(lowercaseLetter)
                }
            })
            console.log(thisCorrectLetters)
            emitter.emit('nextRow', [
                word.letter1 +
                    word.letter2 +
                    word.letter3 +
                    word.letter4 +
                    word.letter5,
                thisCorrectLetters,
            ])
        })

        // function getAllIndexes(arr, val) {
        //     var indexes = [],
        //         i
        //     for (i = 0; i < arr.length; i++) if (arr[i] === val) indexes.push(i)
        //     return indexes
        // }

        console.log(typeof this.completeWord)
        console.log(this.completeWord)
        console.log(this.row)
    },
}
</script>

<style>
.wordle-row-container {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    height: 100%;
}

form {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    height: 100%;
}

.submit-button {
    position: absolute;
    display: none;

    right: 500px;
    height: 60px;
    width: 150px;
}

.shake {
    animation: shake 0.82s cubic-bezier(0.36, 0.07, 0.19, 0.97) both;
    transform: translate3d(0, 0, 0);
    backface-visibility: hidden;
    perspective: 1000px;
}

@keyframes shake {
    10%,
    90% {
        transform: translate3d(-1px, 0, 0);
    }

    20%,
    80% {
        transform: translate3d(2px, 0, 0);
    }

    30%,
    50%,
    70% {
        transform: translate3d(-4px, 0, 0);
    }

    40%,
    60% {
        transform: translate3d(4px, 0, 0);
    }
}
</style>
