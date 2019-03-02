<template>
    <div class="vue-rectangle-reveal">
        <div class="element-wrapper">
            <div class="element" ref="element">
                <slot></slot>
            </div>
            <div class="rectangle" ref="rectangle" :style="{ backgroundColor : color }"></div>
        </div>
    </div>
</template>

<script>

    import { TimelineLite } from "gsap/TweenMax";
    import CustomEase from "./CustomEase";

    export default {
        name: "vue-rectangle-reveal",
        props: {
            duration: {
                type: Number,
                default: 1.5
            },
            delay: {
                type: Number,
                default: 0
            },
            easing: {
                type: String,
                default: "1.000, 0.000, 0.000, 1.000" //easeInOutExpo
            },
            color: {
                type: String,
                default: "black"
            },
            autoPlayAnimation: {
                type: Boolean,
                default: true
            },
            direction: {
                type: String,
                default: "right"
            }
        },
        data() {
            return {
                currentAnimation: null,
                resolve: null,
                animationDelay: this.delay,
                animationDuration: this.duration,
                animationDirection: this.direction,
            }
        },
        computed: {
            rectangle() {
                return this.$refs.rectangle;
            },
            element() {
                return this.$refs.element;
            },
            scaleToAnimate(){
                if(["left", "right"].indexOf(this.animationDirection) > -1){
                    return "scaleX";
                }
                return "scaleY";
            },
            transformOriginToAnimate(){
                if(this.animationDirection === "right"){
                    return ["left","right"]
                } else if (this.animationDirection === "left"){
                    return ["right","left"]
                } else if (this.animationDirection === "top"){
                    return ["100% 100%","0 0"]
                } else if (this.animationDirection === "bottom"){
                    return ["0 0","100% 100%"]
                }
            },
            animation() {
                const tl = new TimelineLite({
                    paused: true,
                    onComplete: this.onAnimationEnd,
                    onReverseComplete : this.onAnimationEnd
                });

                CustomEase.create("customEase", this.easing);

                tl.set(this.element, {clearProps: "all"})
                    .to(this.element, 0, {opacity: 0})
                    .to(this.rectangle, 0,
                        {opacity: 1, transformOrigin: this.transformOriginToAnimate[0], [this.scaleToAnimate]: 0})
                    .to(this.rectangle, this.animationDuration / 2,
                        {opacity: 1, transformOrigin: this.transformOriginToAnimate[0], [this.scaleToAnimate]: 1, ease: "customEase"})
                    .to(this.element, 0,
                        {opacity: 1})
                    .to(this.rectangle, this.animationDuration / 2,
                        {transformOrigin: this.transformOriginToAnimate[1], [this.scaleToAnimate]: 0, ease: "customEase"});

                return tl;
            }
        },
        methods: {
            onAnimationEnd(){
                this.resolve();
                this.$emit("animationEnd");
            },
            animateOut() {
                setTimeout(() => {
                    this.animation.reverse(0);
                }, this.animationDelay * 1000);

                this.currentAnimation = this.createAnimationPromise();
                return this.currentAnimation;
            },
            animateIn() {
                setTimeout(() => {
                    this.animation.play();
                }, this.animationDelay * 1000);

                this.currentAnimation = this.createAnimationPromise();
                return this.currentAnimation;
            },
            createAnimationPromise() {
                return new Promise((resolve) => {
                    this.resolve = resolve;
                });
            },
            setDelay(delay) {
                this.animationDelay = delay;
                return this;
            },
            setDuration(duration) {
                this.animationDuration = duration;
                return this;
            }
        },
        mounted() {
            if (this.autoPlayAnimation) {
                this.animateIn();
            }
        }
    }

</script>

<style>

    .vue-rectangle-reveal {
        display: flex;
    }

    .element-wrapper {
        position: relative;
    }

    .element {
        opacity: 0;
        will-change: opacity;
        display: flex;
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

</style>