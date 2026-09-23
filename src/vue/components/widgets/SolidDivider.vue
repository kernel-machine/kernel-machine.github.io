<template>
    <hr class="divider"/>
</template>

<script setup>
const props = defineProps({})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

hr.divider {
    @include generate-dynamic-styles-with-hash((
        xxxl:   (width: 50px, height: 4px, margin-top: 0.5rem),
        md:     (width: 40px, height: 3px, margin-top: 0)
    ));

    padding: 0;
    margin: 0;
    background: linear-gradient(90deg, $primary, lighten($primary, 15%));
    border: none;
    border-radius: 9999px;
    opacity: 1;
}
</style>