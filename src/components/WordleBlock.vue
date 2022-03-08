<template>
    <div>
        <div v-if="typeof completeLetter === 'string'">
            <p
                class="wordle-block back"
                :class="[
                    correctClass,
                    partiallyCorrectClass,
                    completeClass,
                    animation,
                ]"
                style="text-transform: uppercase"
            >
                {{ completeLetter }}
            </p>
            <div class="wordle-block front" :class="[animation2]"></div>
        </div>
        <input
            v-else-if="typeof row === 'number'"
            v-model="letterInput"
            ref="documentLetterInput"
            class="wordle-block"
            type="text"
            maxlength="1"
            style="text-transform: uppercase"
            @keydown.left="previous()"
            @keydown.right="next()"
            v-on:keypress="changeCurrentValue($event)"

            placeholder=" "
        />

        <div v-else class="wordle-block" />
    </div>
</template>

<script>
export default {
    name: 'WordleBlock',
    props: {
        letter: {
            type: String,
            required: false,
        },

        index: {
            type: Number,
            required: false,
        },
        row: {
            type: Number,
            required: false,
        },
        correctLetter: {
            type: String,
            required: false,
        },
        correctLetters: {
            type: Array,
            required: false,
        },
        currentRow: {
            type: Number,
            required: false,
        },
        completeLetter: {
            type: String,
        },
    },
    data: function () {
        return {
            letterInput: '',
            correctClass: '',
            partiallyCorrectClass: '',
            completeClass: '',
            animation: '',
            animation2: '',
            letterInputLower: '',
        }
    },
    methods: {
        nextOrSubmit: function () {
            this.emitter.emit('submitWord', this.row)
        },
        next: function () {
            if (this.index < 4) {
                this.$refs.documentLetterInput.parentElement.nextElementSibling.children[0].focus()
            }
        },
        previous: function () {
            console.log(this.$refs.documentLetterInput)
            if (this.index > 0) {
                this.$refs.documentLetterInput.parentElement.previousElementSibling.children[0].focus()
            }
        },
        checkIfNext: function () {
            setTimeout('', 25)
            if (this.letterInput.length === 1) {
                this.$refs.documentLetterInput.parentElement.nextElementSibling.children[0].focus()
            }
        },
        isLetter: function (e) {
            let char = String.fromCharCode(e.keyCode) // Get the character
            if (/^[A-Za-z]+$/.test(char)) return true
            // Match with regex
            else e.preventDefault() // If not match, don't add to input text
        },
        changeCurrentValue: function (e) {
            console.log(e.keyCode)
            if (e.keyCode === 13) {
                return this.nextOrSubmit()
            }
            if (e.keyCode === 9) {
                return this.next()
            }

            if (e.keyCode === 39) {
                return this.next()
            }

            if (e.keyCode === 37) {
                return this.previous()
            }

            let char = String.fromCharCode(e.keyCode)
            console.log(e)
            let nextElement = false
            if (this.letterInput.length === 0) {
                nextElement = true
            }

            if (/^[A-Za-z]+$/.test(char)) {
                console.log(this.$refs.documentLetterInput.value)
                console.log(this.letterInput)
                if (nextElement) {
                    this.next()
                }

                return (this.letterInput = char)
            }
            
            e.preventDefault() // If not match, don't add to input text
        },
    },
    mounted: function () {
        let emitter = this.emitter
        let letterInput = this.letterInput
        let index = this.index
        emitter.on('wordSubmitted', function () {
            emitter.emit('letterReturned', {
                letter: letterInput,
                index: index,
            })
        })
        console.log(this.row)
        if (typeof this.correctLetter !== 'undefined') {
            console.log(this.correctLetter)
        }

        if (
            typeof this.correctLetter !== 'undefined' &&
            typeof this.completeLetter !== undefined
        ) {
            if (
                this.correctLetter.toLowerCase() ===
                this.completeLetter.toLowerCase()
            ) {
                this.correctClass = 'correct'
            }
            console.log(this.correctLetters)
            if (
                this.correctLetters.includes(this.completeLetter.toLowerCase())
            ) {
                this.partiallyCorrectClass = 'partially-correct'
            }
            this.completeClass = 'incorrect'
            if (this.currentRow == this.row + 1) {
                this.animation = 'flips'
                this.animation2 = 'flips2'
            }
        }
    },
    watch: {
        letterInput: function (newValue, oldValue) {
            if (newValue !== oldValue) {
                this.letterInputLower = this.letterInput.toLowerCase()
            }
        },
    },
}
</script>

<style>
.wordle-block-container {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
}

.wordle-block {
    min-width: 60px;
    min-height: 60px;
    max-width: 60px;
    max-height: 60px;
    border: 2px solid #ffffff;
    margin: 3px;
    display: flex;
    justify-content: center;
    align-items: center;
}

input {
    height: 100%;
    width: 100%;
    z-index: 99;
    font-size: 35px;
    text-align: center;
    background-color: #ffffff44;
    color: #ffffff;
}

input:placeholder-shown {
    border-color: #e0e0e0;
}
p {
    font-size: 35px;
    vertical-align: middle;
    line-height: 35px;
}
.incorrect {
    background: linear-gradient(
        37deg,
        rgba(99, 99, 99, 0.3) 0%,
        rgba(143, 142, 142, 0.3) 100%
    );
    color: white;
    filter: blur(2px) !important;
}
.partially-correct {
    background: linear-gradient(
        37deg,
        rgba(238, 255, 0, 0.4) 0%,
        rgba(255, 230, 0, 0.7) 100%
    );
    color: white;
    filter: blur(1px) !important;
}
.correct {
    background: linear-gradient(
        37deg,
        rgba(77, 255, 210, 0.466) 0%,
        rgba(1, 252, 156, 0.384) 100%
    ) !important;
    color: white;
    filter: blur(0px) !important;
}

.wordle-block {
    filter: blur(5px);
    transition: filter 0.5s ease;
}

.wordle-block:focus {
    filter: blur(0px);
}

.back {
    position: relative;
    z-index: 9999;
}

.front {
    position: absolute;
    background-color: #ffffff44;
    color: #ffffff;
    transform: translateY(-70px);
}

.flips {
    animation: expand 0.5s forwards;
    transform: scaleX(0);
}

.flips2 {
    animation: contract 0.5s forwards;
    transform: translateY(-70px);
}

/* * {
    filter: blur(0px) !important;
} */

@keyframes expand {
    0% {
        transform: scaleX(0);
    }
    50% {
        transform: scaleX(0);
        display: unset;
    }
    100% {
        transform: scaleX(1);
        display: unset;
    }
}

@keyframes contract {
    0% {
        transform: scaleX(1) translateY(-70px);
    }
    50% {
        transform: scaleX(0) translateY(-70px);
    }
    100% {
        transform: scaleX(0) translateY(-70px);
    }
}
input:not(:placeholder-shown) {
    border: 2px solid #ffffff;
    filter: blur(1px);
}
</style>
