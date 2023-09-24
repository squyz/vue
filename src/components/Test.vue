<template>
    <h4 @click="emitTest">
        Test vue!
    </h4>
</template>

<script>
export default {
    methods: {
        emitTest(){
            this.$emit('emittest', 'Hello!')
        }
    }
}
</script>

<style scoped>

</style>