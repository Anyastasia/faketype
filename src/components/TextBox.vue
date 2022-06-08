<template>
    <div id="word-container" @keyup.tab.enter="resetWords()">
        <div id="word-center">  
            <Word v-for="(word) in words" :id="word.id" :class="{'active': word.active, correct: word.correct, incorrect:word.incorrect}"  :word="word.word" :key="word.id">
            </Word>
        </div>
        <p>{{currentSeconds}}</p>
        <div class="input-box">
            <input type="text" :class="inputClass" ref="inputBox" :id="input" @keyup="checkInput()"  v-model.trim="keyInput" @keypress.once="setTimeout(() => {
               setCountdown() 
            }, 100);">
            <button @click="resetWords()">Reset</button>
        </div>
        <p v-if="done">Raw: {{raw}}</p>
        <p v-if="done">Net: {{net}}</p>
        <p v-if="done">Accuracy: {{accuracy}}&#37</p> 
    </div>
</template>

<script>
import * as fw from 'fake-words'
import Word from '../components/Word.vue'
import english_1k from '../../public/english_1k.json' 

export default {
    name: 'TextBox',
    props: {
        mode: String,
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
            currentSeconds: 60,
            done: false,
            timerReset: false,
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
        async getWords() {
            try {
                let arrWords = []
                for (let i = 0; i < this.totalWords; i++) {
                    // let randomWords = await fetch('https://random-words-api.vercel.app/word')
                    let randomWords = await fetch('https://randomwordgenerator.com/fake-word.php')
                    let [result] = await randomWords.json()
                    arrWords.push(result.word.toLowerCase())
                    this.words.push(arrWords[i])
                }
            } catch (e) {
                console.error(e)
            }
        },

        generate(){
            for (let i = 0; i < this.totalWords; i++) {
                let obj
                if (this.mode == 'fake')
                    obj = {'id': i, 'word': fw.word().toLowerCase()}
                else
                    obj = {'id': i, 'word': english_1k.words[Math.floor(Math.random() * 997)]}

                if (i == 0)
                    obj.active = 1
                // let randomWords = await fetch('https://random-words-api.vercel.app/word')
                //arrWords.push(fw.word().toLowerCase())
                this.words.push(obj)
            }           
        },

        resetWords() {
            this.words = []
            // for (let i = 0; i < 20; i++) {
            //     let obj = {'id': i, 'word': fw.word().toLowerCase(), 'active': 0}
            //     // let randomWords = await fetch('https://random-words-api.vercel.app/word')
            //     //arrWords.push(fw.word().toLowerCase())
            //     //this.words.push(arrWords[i])
            //     this.words.push(obj)
            // }
            // //this.wordCopy = this.words
            this.generate()
            this.currentIndex = 0
            this.inputIncorrect = false
            this.incorrectWords = 0
            this.correctCharacters = 0
            this.incorrectCharacters = 0
            this.totalCharacters = 0
            this.done = false
            this.currentSeconds = 60
            clearInterval(window.timer)
            this.$refs.inputBox.removeEventListener('keypress', this.setCountdown)
            this.$refs.inputBox.addEventListener('keypress', this.setCountdown, {once: true})
            this.$refs.inputBox.disabled = false
            this.keyInput = ""
        },      

        checkInput() {

            const index = this.currentIndex
            const yword = this.words[index].word.slice(0, this.keyInput.length)
            if (this.keyInput != yword) {
                console.log(this.keyInput)
                this.inputIncorrect = true
            } else {
                this.inputIncorrect = false
            }
            if (this.currentIndex == this.totalWords - 1 && this.keyInput.length >= this.words[this.currentIndex].word.length) {
                this.$refs.inputBox.disabled = true
                setTimeout(() => {
                    this.skip()
                }, 10)
                //disable input text
            }
            if (event.keyCode == 32 && this.keyInput.length > 0)
                setTimeout(() => {
                    this.skip()
                }, 10)
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
            this.totalCharacters += (this.currentIndex == this.totalWords - 1) ?  this.keyInput.length : this.keyInput.length + 1// plus 1 for space except for the final word
            this.keyInput = null
            if (this.currentIndex == this.totalWords - 1) {
                this.done = true
                this.words[this.currentIndex].active = 0
            } else {
                this.words[this.currentIndex].active = 0
                this.currentIndex++
                this.words[this.currentIndex].active = 1
            }
        },

        setCountdown() {
            window.timer = setInterval(()=>{
                if (this.currentSeconds > 0 && this.done == false) {
                    this.currentSeconds--
                } else {    
                    this.done = true
                    clearInterval(timer)
                    this.$refs.inputBox.removeEventListener('keypress', this.setCountdown)
                    this.$refs.inputBox.addEventListener('keypress', this.setCountdown, {once: true})
                }
            }, 1000)
        },
        calculateResults() {
            // raw: totalCharacter / 5
            // net: raw - incorrect / time taken
            this.timeTaken = (Math.abs(this.currentSeconds - 60)) / 60
            let errorRate = this.incorrectCharacters / this.timeTaken
            this.raw = Math.round((this.totalCharacters / 5) / this.timeTaken)
            this.net = Math.round(this.raw - errorRate)
            if (this.net < 0) this.net = 0
            this.accuracy = Math.round(Math.abs(((this.incorrectCharacters / this.totalCharacters) - 1) * 100))
        },
    },

    created() {
        this.generate()
    },

    watch: {
        mode(){
            this.resetWords()
        },
        done() {
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
        background-color: var(--text-center);
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

</style>cl