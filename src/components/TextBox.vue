<template>
    <div id="word-container" @keyup.enter="resetWords()">
        <div id="word-center">  
            <Word v-for="(word) in words" :id="word.id" :class="{'active': word.active, correct: word.correct, incorrect:word.incorrect}"  :word="word.word" :key="word.id">
            </Word>
        </div>
        <p>{{currentSeconds}}</p>
        <div class="input-box">
            <input type="text" :class="{'input-incorrect': inputIncorrect}" ref="inputBox" @keydown="checkInput" @keyup="checkCurrentString" v-model.trim="keyInput" @keypress="setCountdown">
            <button @click="resetWords">Reset</button>
        </div>
        <p v-if="done">Raw: {{raw}}</p>
        <p v-if="done">Net: {{net}}</p>
        <p v-if="done">Accuracy: {{accuracy}}&#37</p> 
    </div>
</template>

<script>
import * as fw from 'fake-words'
import Word from './Word.vue'
import english_1k from './json/english_1k.json' 

export default {
    name: 'TextBox',
    props: {
        mode: String,
        numberOfWords: Number,
        timer: Number,
    },
    data() {
        return {
            words: [],
            inputIncorrect: false,
            incorrect: false,
            correct: false,
            active: false,
            keyInput: "",
            currentIndex: 0,
            incorrectWords: 0,
            correctCharacters: 0,
            incorrectCharacters: 0,
            totalCharacters: 0,
            wpm: 0,
            rawWpm: 0,
            accuracy: 0,
            done: false,
            timerReset: false,
            timerRunning: false,
            currentSeconds: 0,
            raw: 0,
            net: 0, 
            accuracy: 0,
            timeTaken: 0,
            totalWords: 25,
        }
    },
    components: {
        Word,
    },
    computed: {
        inputClass() {
            return {
                'input-incorrect': this.inputIncorrect,
            }
        },
    },

    methods: {
        generate(){
            for (let i = 0; i < this.numberOfWords; i++) {
                let obj
                if (this.mode == 'fake')
                    obj = {'id': i, 'word': fw.word().toLowerCase()}
                else
                    obj = {'id': i, 'word': english_1k.words[Math.floor(Math.random() * 997)]}

                if (i == 0)
                    obj.active = 1
                this.words.push(obj)
            }           
        },

        resetWords() {
            this.keyInput = ''
            this.words = []
            this.generate()
            this.currentIndex = 0
            this.inputIncorrect = false
            this.incorrectWords = 0
            this.correctCharacters = 0
            this.incorrectCharacters = 0
            this.totalCharacters = 0
            this.done = false
            this.timerRunning = false
            this.currentSeconds = this.timer
            clearInterval(window.timer)
            this.$refs.inputBox.removeEventListener('keypress', this.setCountdown)
            this.$refs.inputBox.addEventListener('keypress', this.setCountdown, {once: true})
            this.$refs.inputBox.removeAttribute('disabled')
        },      


        checkCurrentString() {
            const index = this.currentIndex
            const yword = this.words[index].word.slice(0, this.keyInput.length)
            this.inputIncorrect = (this.keyInput != yword) ? true : false

            if (this.currentIndex == this.numberOfWords - 1 && this.keyInput.length >= this.words[this.currentIndex].word.length) {
               this.skip()
            }

        },
        checkInput() {
            if (this.currentIndex == this.numberOfWords - 1 && this.keyInput.length >= this.words[this.currentIndex].word.length) {
               this.skip()
            }

            if (event.keyCode == 32 && this.keyInput.length > 0) {
                event.preventDefault()
                this.skip()
            }

        }, 

        skip() {
            const yword = this.words[this.currentIndex].word
            if (this.keyInput == yword) {
                this.words[this.currentIndex].correct = true
            } else{
                this.words[this.currentIndex].incorrect = true
                for (let x = 0; x < this.keyInput.length; x++) {
                    if (this.keyInput[x] != yword[x])  this.incorrectCharacters++
                }
            }
            this.totalCharacters += (this.currentIndex == this.numberOfWords - 1) ?  this.keyInput.length : this.keyInput.length + 1// plus 1 for space except for the final word
            
            if (this.currentIndex == this.numberOfWords - 1) {
                this.done = true
                this.words[this.currentIndex].active = 0
            } else {
                this.words[this.currentIndex].active = 0
                this.currentIndex++
                this.words[this.currentIndex].active = 1
            }
            this.keyInput = ''
        },

        setCountdown() {
            if (this.timerRunning == false) {
                window.timer = setInterval(()=>{
                    if (this.currentSeconds > 0 && this.done == false) {
                        this.currentSeconds--
                    } else {    
                        this.done = true
                        clearInterval(timer)
                    }
                }, 1000)
                this.timerRunning = true
            }
        },
        calculateResults() {
            // raw: (totalCharacter / 5) / time taken
            // error rate: incorrect chars / time taken
            // net: raw - error rate
            this.timeTaken = (Math.abs(this.currentSeconds - this.timer)) / this.timer
            let errorRate = this.incorrectCharacters / this.timeTaken
            this.raw = Math.round((this.totalCharacters / 5) / this.timeTaken)
            this.net = Math.round(this.raw - errorRate)
            if (this.net < 0) this.net = 0
            this.accuracy = Math.round(Math.abs(((this.incorrectCharacters / this.totalCharacters) - 1) * 100))
        },
    },

    created() {
        this.currentSeconds = this.timer
        this.generate()
    },

    watch: {
        mode(){
            this.resetWords()
        },
        timer() {
            this.resetWords()
        },
        numberOfWords() {
            this.resetWords()
        },
        done() {
            if (this.done === true)
                this.$refs.inputBox.setAttribute('disabled', true)
            this.calculateResults()
        }
    }

    
}
</script>


<style scoped>

    p {
        display: inline;
        margin-right: 1rem;
    }
    #word-container {
        display: flex;
        flex-direction: column;
        justify-content: center;
        background-color: inherit;
        align-items: center;
        padding: 1.5rem;
        min-width: 45%;
        width: 45%;
        border-radius: 10px;
    }  

    .input-box {
        display: flex;
        justify-content: center;
        width: 90%;
        padding: 1rem;
    }
    

    input[type=text] {
        width: 100%;
        min-width: 100%;
        font: inherit;
        padding: 0.5rem 0.5rem;
        border-radius: 4px;
        border: none;
    }

    button {
        font: inherit;
        padding-inline: 0.5rem;
        margin-inline-start: 1.2rem;
    }

    #word-center {
        padding: 1rem 1rem;
        font-size: 1rem;
    }
    .word {
        display: inline;
        margin-inline-end: 1rem;
    }

    .letter {
        font-size: large;
    }


    .active {
        color: violet;
    }

    .incorrect {
        color: red;
    }

    
    .input-incorrect {
        background-color: #FF7F7F;
        /* background-color: #ff6d91 */
    }

    .correct {
        color: green;
    }

    input[type='text'] {
        border-radius: 4px;
    }

</style>