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
                resolve: null,
            }
        },
        computed : {
            rectangle(){
                return this.$refs.rectangle;
            },
            element(){
                return this.$refs.element;
            },
            wrapper(){
                return this.$refs.wrapper;
            }
        },
        methods: {
            animateOut() {
                this.wrapper.classList.add("animate-out");
                this.currentAnimation = this.createAnimationPromise();
                return this.currentAnimation;
            },
            animateIn() {
                this.wrapper.classList.add("animate-in");
                this.currentAnimation = this.createAnimationPromise();
                return this.currentAnimation;
            },
            createAnimationPromise() {
                return new Promise((resolve, reject) => {
                    this.resolve = resolve;
                });
            },
            setDelay(delay){
                this.rectangle.style.animationDelay = delay + "s";
                this.element.style.animationDelay = delay + "s";
                return this;
            },
            setDuration(duration){
                this.rectangle.style.animationDuration = duration + "s";
                this.element.style.animationDuration = duration + "s";
                return this;
            }
        },
        mounted() {
            this.setDelay(this.delay);
            this.setDuration(this.duration);

            this.rectangle.style.backgroundColor = this.color;

            this.rectangle.style.animationTimingFunction = this.easing;

            if(this.autoPlayAnimation){
                this.animateIn();
            }

            this.rectangle.addEventListener("animationend", () => {
                if (this.wrapper.classList.contains("animate-in")) {
                    this.element.style.opacity = 1;
                } else if (this.wrapper.classList.contains("animate-out")) {
                    this.element.style.opacity = 0;
                }
                this.wrapper.classList.remove("animate-in");
                this.wrapper.classList.remove("animate-out");

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