<template>
<div>
     <lottie :options="defaultOptions" :height="400" :width="400" v-on:animCreated="handleAnimation"/>
        <div>
            <p>Speed: x{{animationSpeed}}</p>
            <input type="range" value="1" min="0" max="3" step="0.5"
                   v-on:change="onSpeedChange" v-model="animationSpeed">
        </div>
</div>
</template>

<script>
import Lottie from './lottie.vue';
export default{
    name: 'lottie',
    components: {
      'lottie': 'https://assets3.lottiefiles.com/packages/lf20_W6nHGZ/Whats app.json'
    },
}
</script>