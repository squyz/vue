<template>
    <input @input="emitTest" :value="input"/>
</template>

<script>
export default {
    props: {
        input: {
            type: String,
            default: 'hello',
        }
    },
    methods: {
        emitTest(event){
            this.$emit('update:input', event.target.value)
        }
    }
}
</script>

<style scoped>

</style>