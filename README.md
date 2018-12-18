# vue-rectangle-reveal

> A Vue.js 2.0 component to reveal elements with a trending rectangle shape animation

## Demo

<p align="center"> 
    <img src="./src/assets/demo.gif" width="400px" height="auto" alt="Demo gif"/>
</p>

## Live demo

Coming soon

## Installation

#### NPM

```bash
 npm i --save vue-rectangle-reveal
 ```

## Example

:information_desk_person: You can reveal whatever element you want, whether they are texts, images and so on...
Here is the easiest way to use this component :
```vue
    <template>
        <vue-rectangle-reveal>
            <h1>My text to reveal</h1>
        </vue-rectangle-reveal>
    </template>
        
    <script>
        import VueRectangleReveal from "vue-rectangle-reveal";
        
        export default {
            components: {
                VueRectangleReveal,
            },
        };
    </script>
```

What is in the default slot of the component will be invisible at the beginning, then covered by the animating rectangle shape, to eventually be revealed.

Here is a more complete example if you want to control the behavior of the component the way you like :ok_woman: :
```vue
    <template>
        <vue-rectangle-reveal ref="imageRectangleReveal" 
                              :delay="0.5" 
                              :duration="1.5" 
                              color="#d7a19e">
            <img src="path/to/my-image.jpg" width="700px" height="auto"/>
        </vue-rectangle-reveal>
    </template>
        
    <script>
        import VueRectangleReveal from "vue-rectangle-reveal";
        
        export default {
            components: {
                VueRectangleReveal,
            },
            mounted(){
                setTimeout(async() => {
                    await this.$refs.imageRectangleReveal
                        .setDelay(0.3) // set the delay on the fly
                        .setDuration(1.2) // set the duration on the fly
                        .animateOut();
                    
                    //Do something when the animation is done
                    console.log("done")
                },6000)
            }
        };
    </script>
```

## Props

| name         |  type  | description                                                      | required | default value |
|--------------|:------:|------------------------------------------------------------------|----------|---------------|
|duration        | Number  |The duration of the animation in seconds      |false      |1.2   |
|delay        |Number   |The delay of the animation in seconds      |false      |0   |
|easing        |String   |The easing to use for the animation      |false      |"cubic-bezier(1.000, 0.000, 0.000, 1.000)" /* easeInOutExpo*/   |
|color        |String   |The color of the rectangle shape, with the css color format      |false      |"black"   |
|autoPlayAnimation        |Boolean   |Whether the animation is to be played automatically      |false      |true   |

## Methods

| Name                    | Description             |Params|Returns
|-------------------------|-------------------------|----------|----------|
|animateIn          |Play the animate-in animation    |      |a Promise resolved when the animation is done    |
|animateOut                    |Play the animate-out animation     |      |a Promise resolved when the animation is done    |
|setDelay(delay)                     |Set the delay of the animation in seconds     |<ul><li>`delay` : the delay to set</li></ul>      |the component so that you can chain its methods    |
|setDuration(duration)         |Set the duration of the animation in seconds     |<ul><li>`duration` : the duration to set</li></ul>     |the component so that you can chain its methods    |

## Events emitted

| Name                    | Description             |
|-------------------------|-------------------------|
|animationEnd               |Fired when the animation is done  |


## Contributing 

Contributions are welcome !
Follow the instructions in the [contributing file](./CONTRIBUTING.md)

## License

[MIT](./LICENCE)

