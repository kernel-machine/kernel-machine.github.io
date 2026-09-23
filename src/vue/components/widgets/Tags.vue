<template>
    <ul class="tags">
        <li class="tag-item text-1" v-for="tag in tags">
            <span v-html="tag.label || tag"/>
        </li>
    </ul>
</template>

<script setup>
const props = defineProps({
    tags: Object
})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

ul.tags {
    display: block;
    list-style: none;
    padding: 0;
    margin: 0;
    line-height: 20px;
}

li.tag-item {
    display: inline-flex;
    background-color: rgba(5, 150, 105, 0.12);
    color: #047857;
    font-weight: 600;
    letter-spacing: 0.02em;
    border-radius: 9999px;
    text-transform: lowercase;
    white-space: nowrap;
    user-select: none;
    transition: all 0.2s ease-out;

    &:hover {
        background-color: rgba(5, 150, 105, 0.22);
        color: #065f46;
    }

    margin-right: 6px;
    margin-bottom: 6px;
    padding: 3px 12px;

    @include media-breakpoint-down(md) {
        margin-right: 4px;
        margin-bottom: 4px;
        padding: 2px 10px;
    }
}
</style>