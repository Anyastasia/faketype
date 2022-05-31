<template>
<div class="letter">
    {{letter}}
</div>
</template>

<script>


export default {
    data() {
        return {}
    },

    props: {
        letter: String
    }
}

</script>


<style>

.letter {
    display: inline-block;
}
</style>
