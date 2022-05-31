<template>
    <div class="words" :active="active" :incorrect="incorrect" :correct="correct">
        {{word + ' '}}
    </div>
</template>

<script>
import Letter from '../components/Letter.vue';

export default {
    components: {
        Letter
    },
    props: {
        id: Number,
        word: String,
        incorrect: Boolean,
        correct: Boolean,
        active: Number,
    },
    // computed: {
    //     classObject() {
    //         return {
    //             incorrect: this.incorrect,
    //             correct: this.correct,
    //             active: this.active
    //         }
    //     }
    // },
    mounted() {
        console.log(this.incorrect.incorrect)
    }
}
</script>

<style scoped>

.words {
    display: inline;    
}

.active {
 color: var(--active);   
}

.correct {
    color: var(--correct);
}

.incorrect {
    color: var(--incorrect);
}
</style>