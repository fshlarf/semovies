<template>
    <button :class="`btn btn-blue ${btnClass}`" @click="$emit('btn-click')">{{ btnTitle }}</button>
</template>

<script>
export default {
    props: {
        btnTitle: {
            type: String,
            default: 'Submit',
        },
        btnClass: {
            type: String,
            default: ''
        }
    }
}
</script>

<style>

</style>