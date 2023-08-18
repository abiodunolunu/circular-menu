<script setup lang='ts'>
import { gsap } from 'gsap';
import { ref } from 'vue';

const numberOfIcons = ref(6)
const itemSize = ref(32)
const wrapperSize = ref(120)

const open = ref(false)

function toggle() {
    open.value = !open.value
}

function generateDivPositionCSS(divIndex: number, totalDivs: number, wrapperSize: number, itemSize: number) {
    const angleDifference = 360 / totalDivs;
    const initialRotation = -90; // Start from the top center
    const angle = initialRotation + angleDifference * divIndex;

    const radians = (angle * Math.PI) / 180;
    const circleRadius = wrapperSize / 2 - (itemSize / 2) // Adjust this as needed
    const x = Math.cos(radians) * circleRadius;
    const y = Math.sin(radians) * circleRadius;

    return `
  translate(-50%, -50%) translate(${x}px, ${y}px)
  `;
}

function enter(el: Element, done: () => void) {
    gsap.timeline()
        .to('.wrapper', {
            rotate: 0
        })
        .to(el, {
            transform() {
                return generateDivPositionCSS(Number((el as HTMLDivElement).dataset.index), numberOfIcons.value, wrapperSize.value, itemSize.value)
            },
            scale: 1,
            onComplete: done
        }, "=")
}

function leave(el: Element, done: () => void) {
    gsap.timeline()
        .to('.wrapper', {
            rotate: 360
        })
        .to(el, {
            transform() {
                return `translate(-50%, -50%) translate(0px, 0px)`
            },
            onComplete: done
        }, '=')
}


</script>

<template>
    <div class="container">
        <div class="settings">
            <label>
                wrapper size (px):
                <input type="number" v-model="wrapperSize" min="1" />
            </label>
            <label>
                item size (px):
                <input type="number" v-model="itemSize" min="1" />
            </label>
            <label>
                number of icons
                <input type="number" v-model="numberOfIcons" min="1" />
            </label>
        </div>

        <div class="wrapper" :style="{ height: `${wrapperSize}px` }">
            <button class="switch" @click="toggle">
                <span v-if="open">
                    🔓
                </span>

                <span v-else>
                    🔒
                </span>
            </button>

            <TransitionGroup @enter="enter" @leave="leave" appear :key="numberOfIcons + wrapperSize + itemSize">
                <!-- THE KEY WAS SET LIKE THIS FOR DEMONSTRATION PURPOSES, WHEN WORKING, YOU ALREADY KNOW THE SIZES OF YOUR ELEMENTS -->
                <button :data-index="idx" v-if="open" class="item" v-for="i, idx in numberOfIcons" :key="i"
                    :style="{ height: `${itemSize}px` }">
                    {{ idx % 2 === 0 ? '💻' : '📱' }}
                </button>
            </TransitionGroup>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.container {
    background-color: #000;
    height: 100vh;
    display: grid;
    place-items: center;
    padding: 1rem;
}

.wrapper {
    height: 120px;
    aspect-ratio: 1;
    border-radius: 50%;
    display: grid;
    place-items: center;

    position: fixed;
    bottom: 0;
    right: 0;
}

button.switch {
    background-color: white;
    border-radius: 50%;
    font-size: 1.5rem;
    height: 2.5rem;
    aspect-ratio: 1;
    z-index: 10;
    cursor: pointer;
}

.item {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    height: 2rem;
    aspect-ratio: 1;
    outline: 0;
    border: 0;
    border-radius: 50%;
    background-color: orange;
    cursor: pointer;

    &:hover {
        background-color: gold;
    }
}

.settings {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;

    label {
        color: white;
        font-size: 0.75rem;
        margin-left: auto;
        margin-right: auto;
    }

    input {
        display: block;
        height: 2rem;
        font-size: 0.875rem;
        padding: 0.25rem;
    }
}
</style>