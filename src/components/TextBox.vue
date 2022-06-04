<template>
    <div id="word-container" @keyup.tab.enter="resetWords()">
        <div id="word-center">  
            <Word v-for="(word) in words" :id="word.id" :class="{'active': word.active, correct: word.correct, incorrect:word.incorrect}"  :word="word.word" :key="word.id">
            </Word>
        </div>
        <div class="input-box">
            <input type="text" :class="inputClass" :id="input" @keyup="checkInput()" @keyup.space="skip()" v-model.trim="keyInput">
            <button @click="resetWords()">Reset</button>
        </div>
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
                for (let i = 0; i < 20; i++) {
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
            for (let i = 0; i < 20; i++) {
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
            let arrWords = []
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
            this.keyInput = ""
            this.inputIncorrect = false
        },      

        checkInput() {
            const index = this.currentIndex
            let incorrectScore = 0
            const yword = this.words[index].word.slice(0, this.keyInput.length)
            if (this.keyInput != yword) {
                this.inputIncorrect = true
            } else {
                this.inputIncorrect = false
            }
        }, 

        skip() {
            // get current id -> set active to false
            // get incremented id -> set active to true
            // reset input
            const yword = this.words[this.currentIndex].word.slice(0, this.keyInput.length)
            if (this.keyInput != yword)
                this.words[this.currentIndex].incorrect = true
            else if (this.keyInput == yword)
                this.words[this.currentIndex].correct = true
            else 
                this.words[this.currentIndex].incorrect = true
            this.words[this.currentIndex].active = 0
            this.currentIndex++
            this.words[this.currentIndex].active = 1
            this.keyInput = ''
        },
    },

    created() {
        this.generate()
    },

    watch: {
        mode(){
            this.resetWords()
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