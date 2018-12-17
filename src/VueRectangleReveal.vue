<template>
    <div class="vue-rectangle-reveal">
        <div class="element-wrapper" ref="wrapper">
            <div class="element" ref="element">
                <slot></slot>
            </div>
            <div class="rectangle" ref="rectangle"></div>
        </div>
    </div>
</template>

<script>

    export default {
        name: "vue-rectangle-reveal",
        props: {
            duration: {
                type: Number,
                default: 1.2
            },
            delay: {
                type: Number,
                default: 0
            },
            easing: {
                type: String,
                default: "cubic-bezier(1.000, 0.000, 0.000, 1.000)" //easeInOutExpo
            },
            color: {
                type: String,
                default: "black"
            },
            autoPlayAnimation: {
                type: Boolean,
                default: true
            }
        },
        data() {
            return {
                currentAnimation: null,
                resolve: null
            }
        },
        methods: {
            animateOut() {
                const wrapper = this.$refs.wrapper;
                wrapper.classList.add("animate-out");
                this.currentAnimation = this.createAnimationPromise();
                return this.currentAnimation;
            },
            animateIn() {
                const wrapper = this.$refs.wrapper;
                wrapper.classList.add("animate-in");
                this.currentAnimation = this.createAnimationPromise();
                return this.currentAnimation;
            },
            createAnimationPromise() {
                return new Promise((resolve, reject) => {
                    this.resolve = resolve;
                });
            }
        },
        mounted() {
            const wrapper = this.$refs.wrapper;
            const rectangle = this.$refs.rectangle;
            const element = this.$refs.element;

            rectangle.style.animationDelay = this.delay + "s";
            rectangle.style.backgroundColor = this.color;
            element.style.animationDelay = this.delay + "s";

            rectangle.style.animationDuration = this.duration + "s";
            element.style.animationDuration = this.duration + "s";

            rectangle.style.animationTimingFunction = this.easing;

            if(this.autoPlayAnimation){
                this.animateIn();
            }

            rectangle.addEventListener("animationend", () => {
                if (wrapper.classList.contains("animate-in")) {
                    element.style.opacity = 1;
                } else if (wrapper.classList.contains("animate-out")) {
                    element.style.opacity = 0;
                }
                wrapper.classList.remove("animate-in");
                wrapper.classList.remove("animate-out");

                this.resolve();
                this.$emit("animationEnd");
            })
        }
    }

</script>

<style scoped>

    .vue-rectangle-reveal {
        display: flex;
    }

    .element-wrapper {
        position: relative;
    }

    .element {
        opacity: 0;
        will-change: opacity;
        display: flex; /* This is to prevent weird space behavior on images in the slot */
        align-items: baseline;
    }

    .rectangle {
        position: absolute;
        background-color: black;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        opacity: 0;

        will-change: transform-origin, tranform, opacity;
    }

    .animate-in .rectangle {
        animation-name: reveal;
    }

    .animate-in .element {
        animation-name: appear;
        opacity: 0;
    }

    .animate-out .rectangle {
        animation-direction: reverse;
        animation-name: reveal;

    }

    .animate-out .element {
        animation-direction: reverse;
        animation-name: appear;
        opacity: 1;
    }

    @keyframes appear {
        0% {
            opacity: 0;
        }
        50% {
            opacity: 0;
        }
        51% {
            opacity: 1;
        }
        100% {
            opacity: 1;
        }
    }

    @keyframes reveal {
        0% {
            opacity: 1;
            transform-origin: left;
            transform: scaleX(0);
        }
        50% {
            opacity: 1;
            transform-origin: left;
            transform: scaleX(1);
        }
        51% {
            opacity: 1;
            transform-origin: right;
            transform: scaleX(1);
        }
        100% {
            opacity: 1;
            transform-origin: right;
            transform: scaleX(0);
        }
    }

</style>