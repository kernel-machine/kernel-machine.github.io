<template>
    <div class="portfolio-item"
         :class="`portfolio-item-${transitionStatus}`"
         @click="_onClick">
        <div class="portfolio-item-content-wrapper">
            <div v-if="item.img || item.fallbackFaIcon" class="portfolio-item-icon-wrapper">
                <IconView class="portfolio-icon-view"
                          ref="iconView"
                          :img="item?.img"
                          :fa-icon="item?.fallbackFaIcon"
                          :background-color="item?.fallbackFaIconColor"
                          :prioritize-image="true"
                          :transparency="!item"/>

                <div class="portfolio-item-thumb-overlay">
                    <div class="portfolio-item-thumb-overlay-content eq-h6">
                        <i class="fas fa-eye fa-2x"/>
                    </div>
                </div>
            </div>

            <div class="portfolio-item-description-wrapper">
                <button class="portfolio-item-title"
                        v-html="localize(item.locales, 'title')"/>
                <p v-if="item._authors && item._authors.length" class="portfolio-item-author"><a v-for="(a,i) in item._authors" :key="a">
                    <u v-if="a.toLowerCase().includes('giovannesi')">{{ a }}</u>
                    <i v-else>{{ a }}</i>
                    <i v-if="i < item._authors.length -1">, </i>
                </a></p>
                <div v-if="item._year || categoryName" class="portfolio-item-badges">
                    <span v-if="item._year" class="badge-meta badge-year">{{ item._year }}</span>
                    <span v-if="categoryName" class="badge-meta badge-category" v-html="categoryName"/>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import {inject, onMounted, onUnmounted, ref, watch} from "vue"
import {useScheduler} from "/src/composables/scheduler.js"
import {useUtils} from "/src/composables/utils.js"
import IconView from "/src/vue/components/widgets/IconView.vue"

const scheduler = useScheduler()
const utils = useUtils()

const props = defineProps({
    /** @type {ArticleItem} **/
    item: {
        type: Object,
        required: true
    },
    categoryName: String,
    index: Number,
    transitionCount: Number
})

/** @type {Function} */
const localize = inject("localize")

/** @type {Function} */
const showProjectModal = inject("showProjectModal")

const transitionStatus = ref("hidden")
const iconView = ref(null)
const tag = utils.generateUniqueRandomString("portfolio-item")

onMounted(() => { _showAfterLoading() })
onUnmounted(() => { _hide() })
watch(() => props.transitionCount, () => { _showAfterLoading() })

const _hide = () => {
    transitionStatus.value = "hidden"
    scheduler.clearAllWithTag(tag)
}

const _showAfterLoading = () => {
    _hide()

    scheduler.interval(() => {
        
        const isLoading = iconView.value ? (iconView.value.imageView && iconView.value.imageView.isLoading()) : false
        const hasImage = props.item.img
        if(!hasImage || !isLoading) {
            _show()
        }
    }, 1000/30, tag)
}

const _show = () => {
    scheduler.clearAllWithTag(tag)

    const timeout = 30 + (props.index || 0) * 60
    scheduler.schedule(() => {
        transitionStatus.value = "showing"
    }, timeout, tag)

    scheduler.schedule(() => {
        transitionStatus.value = "shown"
        scheduler.clearAllWithTag(tag)
    }, timeout + 350, tag)
}

const _onClick = () => {
    showProjectModal(props.item)
}
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

div.portfolio-item {
    display: flex;
    align-items: start;
    justify-content: center;

    width: 100%;
    height: 100%;
    background-color: #ffffff;
    border: 1px solid rgba(226, 232, 240, 0.85);
    border-radius: 22px;
    box-shadow: 0 4px 6px -1px rgba(15, 23, 42, 0.03), 0 2px 4px -2px rgba(15, 23, 42, 0.02);
    transition: transform 0.25s cubic-bezier(0.16, 1, 0.3, 1), box-shadow 0.25s cubic-bezier(0.16, 1, 0.3, 1), border-color 0.25s ease;

    @include media-breakpoint-down(sm) {
        border-radius: 16px;
    }

    &:hover {
        transform: translateY(-4px);
        border-color: rgba(5, 150, 105, 0.35);
        box-shadow: 0 16px 28px -4px rgba(15, 23, 42, 0.08), 0 6px 12px -4px rgba(15, 23, 42, 0.04);
    }
}

/** ----------------- TRANSITIONS ------------------- **/
div.portfolio-item-hidden {
    opacity: 0;
}

div.portfolio-item-showing {
    animation: appear 0.3s ease-out forwards;
}

@keyframes appear {
    from {
        opacity: 0;
        transform: scale(0.92) translateY(20px);
    }
    to {
        opacity: 1;
        transform: scale(1) translateY(0);
    }
}

/** ------------------- CONTENT ---------------------- **/
div.portfolio-item-content-wrapper {
    --proportion: 0.9;
    --base-icon-size: 180px;
    --base-title-size: 21px;

    @media (max-width: 2000px) {
        --proportion: 0.85;
        --base-title-size: 22px;
    }
    @media (max-width: 1560px) {
        --proportion: 0.775;
        --base-title-size: 22px;
    }
    @include media-breakpoint-down(xxl) {
        --proportion: 0.675;
        --base-title-size: 23px;
    }
    @include media-breakpoint-down(lg) {
        --proportion: 0.7;
    }
    @include media-breakpoint-down(md) {
        --proportion: 0.65;
    }
    @include media-breakpoint-down(sm) {
        --proportion: 0.6;
    }
    @media (max-width: 500px) {
        --proportion: 0.45;
        --base-title-size: 26px;
    }

    display: inline-flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
    cursor: pointer;
    margin: calc(26px * var(--proportion));

    /** Icon View Wrapper **/
    div.portfolio-item-icon-wrapper {
        position: relative;
        margin: 0 auto;
        cursor: pointer;
        overflow: hidden;
        user-select: none;
        pointer-events: none;
        border-radius: 22%;
        aspect-ratio: 1/1;
        width: calc(var(--base-icon-size) * var(--proportion));
        height: calc(var(--base-icon-size) * var(--proportion));
        box-shadow: 0 4px 10px rgba(15, 23, 42, 0.06);
    }

    /** Icon View **/
    div.portfolio-icon-view {
        font-size: calc(var(--base-icon-size)/2 * var(--proportion));
    }

    /** Overlay Feedback **/
    div.portfolio-item-thumb-overlay {
        position: absolute;
        top: 0;
        opacity: 0;

        display: flex;
        align-items: center;
        justify-content: center;

        width: 100%;
        height: 100%;
        border-radius: 22%;

        background: rgba(5, 150, 105, 0.85);
        backdrop-filter: blur(2px);
        transition: opacity 0.25s ease;

        &-content {
            color: $white;
        }
    }

    div.portfolio-item-description-wrapper {
        display: flex;
        flex-direction: column;
        align-items: center;
        width: 100%;
        margin-top: calc(16px * var(--proportion));
    }

    /** Title **/
    button.portfolio-item-title {
        border: none;
        padding: 0;
        background-color: transparent;
        color: #0f172a;
        font-weight: 700;
        font-size: calc(var(--base-title-size) * var(--proportion));
        line-height: 1.35;
        margin-bottom: 6px;
        text-align: center;
        transition: color 0.2s ease;
    }

    p.portfolio-item-category {
        padding: 0;
        color: #64748b !important;
        font-size: calc(var(--base-title-size) * 0.73 * var(--proportion));
        margin: 0;
        text-align: center;
        @include media-breakpoint-up(lg) {
            margin-top: 2px;
        }
    }

    p.portfolio-item-author {
        padding: 0;
        color: #64748b !important;
        font-size: calc(var(--base-title-size) * 0.78 * var(--proportion));
        line-height: 1.4;
        margin: 0 0 6px 0;
        text-align: center;

        u {
            text-decoration-color: $primary;
            text-underline-offset: 3px;
            font-weight: 600;
            color: #334155;
        }
    }

    div.portfolio-item-badges {
        display: flex;
        align-items: center;
        justify-content: center;
        flex-wrap: wrap;
        gap: 6px;
        margin-top: 6px;

        .badge-meta {
            display: inline-flex;
            align-items: center;
            padding: 3px 10px;
            border-radius: 9999px;
            font-size: 0.75rem;
            font-weight: 600;
            letter-spacing: 0.02em;
            line-height: 1.2;
        }

        .badge-year {
            background-color: rgba(5, 150, 105, 0.1);
            color: #059669;
        }

        .badge-category {
            background-color: #f1f5f9;
            color: #475569;
        }
    }
}

div.portfolio-item:hover {
    div.portfolio-item-thumb-overlay {
        opacity: 1;
    }

    button.portfolio-item-title {
        color: $primary;
    }
}
</style>