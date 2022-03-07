<template>
    <div class="wordle-row-container">
        <form @submit.prevent="wordSubmitted()">
            <WordleBlock
                ref="letter1"
                :key="0 + currentRow"
                :letter="letter1"
                :otherLetters="removeLetterFromWord(letter1, word)"
                :index="0"
                :row="row"
                :correctLetter="word.split('')[0]"
                :correctLetters="correctLetters"
                :currentRow="currentRow"
                :completeLetter="completeLetters[0]"
            />
            <WordleBlock
                ref="letter2"
                :key="1 + currentRow"
                :letter="letter2"
                :index="1"
                :row="row"
                :correctLetter="word.split('')[1]"
                :correctLetters="correctLetters"
                :currentRow="currentRow"
                :completeLetter="completeLetters[1]"
            />
            <WordleBlock
                ref="letter3"
                :key="2 + currentRow"
                :letter="letter3"
                :index="2"
                :row="row"
                :correctLetter="word.split('')[2]"
                :correctLetters="correctLetters"
                :currentRow="currentRow"
                :completeLetter="completeLetters[2]"
            />
            <WordleBlock
                ref="letter4"
                :key="3 + currentRow"
                :letter="letter4"
                :index="3"
                :row="row"
                :correctLetter="word.split('')[3]"
                :correctLetters="correctLetters"
                :currentRow="currentRow"
                :completeLetter="completeLetters[3]"
            />
            <WordleBlock
                ref="letter5"
                :key="4 + currentRow"
                :letter="letter5"
                :index="4"
                :row="row"
                :correctLetter="word.split('')[4]"
                :correctLetters="correctLetters"
                :currentRow="currentRow"
                :completeLetter="completeLetters[4]"
            />
        </form>
    </div>
</template>

<script>
import WordleBlock from './WordleBlock.vue'

export default {
    name: 'WordleRow',
    components: { WordleBlock },
    props: {
        word: {
            type: String,
            required: true,
        },
        row: {
            type: Number,
            required: true,
        },
        currentRow: {
            type: Number,
            required: true,
        },
        completeWord: {
            type: String,
        },
        correctLetters: {
            type: Array,
            required: true,
        },
    },
    data: function () {
        return {
            letter1: this.word.substring(0, 1),
            letter2: this.word.substring(1, 2),
            letter3: this.word.substring(2, 3),
            letter4: this.word.substring(3, 4),
            letter5: this.word.substring(4),

            completeLetters: this.completeWord.split(''),
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
        console.log('Component mounted.')
        console.log(this.word)
        let emitter = this.emitter
        let refs = this.$refs
        let thisRow = this.row
        let thisWord = this.word
        let thisCorrectLetters = this.correctLetters

        emitter.on('submitWord', function (row) {
            if (row !== thisRow) {
                return
            }
            let word = {}
            // let numberOfSubmissions = 0
            word.letter1 = refs.letter1.letterInput
            word.letter2 = refs.letter2.letterInput
            word.letter3 = refs.letter3.letterInput
            word.letter4 = refs.letter4.letterInput
            word.letter5 = refs.letter5.letterInput
            console.log(word)
            Object.keys(word).forEach((letter) => {
                let lowercaseLetter = word[letter].toLowerCase()
                if (thisWord.includes(lowercaseLetter)) {
                    thisCorrectLetters.push([
                        lowercaseLetter,
                        getAllIndexes(thisWord, lowercaseLetter),
                    ])
                }
            })
            console.log(thisCorrectLetters)
            emitter.emit(
                'nextRow',
                word.letter1 +
                    word.letter2 +
                    word.letter3 +
                    word.letter4 +
                    word.letter5
            )
        })

        function getAllIndexes(arr, val) {
            var indexes = [],
                i
            for (i = 0; i < arr.length; i++) if (arr[i] === val) indexes.push(i)
            return indexes
        }

        console.log(this.completeLetters)

        function arraysEqual(arr1, arr2) {
            if (arr1.length !== arr2.length) return false
            for (var i = arr1.length; i--; ) {
                if (arr1[i] !== arr2[i]) return false
            }

            return true
        }

        if (arraysEqual(this.word, this.completeLetters)) {
            console.log('Word is complete')
            emitter.emit('finish')
        }
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

form div:nth-child(1) div * {
    animation-delay: 0s;
}
form div:nth-child(2) div * {
    animation-delay: 0.25s;
}
form div:nth-child(3) div * {
    animation-delay: 0.5s;
}
form div:nth-child(4) div * {
    animation-delay: 0.75s;
}
form div:nth-child(5) div * {
    animation-delay: 1s;
}
</style>
