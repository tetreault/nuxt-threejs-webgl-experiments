<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div class="hero-head">
        <h1 class="title has-text-centered">ThreeJS/WebGL with Nuxt</h1>
        <p class="has-text-centered">
          The source code can be found
          <a
            href="https://github.com/tetreault/nuxt-threejs-webgl-experiments"
            target="_blank"
            title="Github Repo"
          >here</a>
        </p>
      </div>
      <div class="hero-body">
        <ul>
          <li>
            <h4>Experiments</h4>
          </li>
          <li>
            <a href="/experiments/camera-texture">Camera Texture</a>
          </li>
          <li>
            <a href="/experiments/patchwork">Patchwork (lines)</a>
          </li>
          <li>
            <a href="/experiments/video-on-text">Video on text</a>
          </li>
          <li>
            <a href="/experiments/ascii-video-texture">ASCII video texture</a>
          </li>
          <li>
            <a href="/experiments/photo-particles">Photo Particles</a>
          </li>
          <li>
            <a href="/experiments/photo-particles-part-2">Photo Particles Part 2</a>
          </li>
          <li>
            <a href="/experiments/webcam-particles">Webcam Particles</a>
          </li>
          <li>
            <a href="/experiments/retro-geometry">Retro Geometry</a>
          </li>
          <li>
            <a href="/experiments/audio-visuals-clearwater">Audio Visuals Clearwater (by JTH)</a>
          </li>
        </ul>
        <ul>
          <li>
            <h4>Learn Three.js Book demos</h4>
          </li>
          <li>
            <a href="/learn-threejs-book/first-scene">First Scene</a>
          </li>
          <li>
            <a href="/learn-threejs-book/materials-light">Materials Light</a>
          </li>
          <li>
            <a href="/learn-threejs-book/materials-light-animation">Materials Light Animation</a>
          </li>
          <li>
            <a href="/learn-threejs-book/add-cubes">Add Cubes</a>
          </li>
          <li>
            <a href="/learn-threejs-book/manual-geometry">Manual Geometry</a>
          </li>
          <li>
            <a href="/learn-threejs-book/house-tree-scene">Scene with House and Tree</a>
          </li>
        </ul>
        <ul>
          <li>
            <h4>Mozilla WebGL demos</h4>
          </li>
          <li>
            <a href="/mozilla-demos/boilerplate">Simple boilerplate</a>
          </li>
          <li>
            <a href="/mozilla-demos/simple-color-animation">Simple color animation</a>
          </li>
          <li>
            <a href="/mozilla-demos/color-masking">Color masking</a>
          </li>
          <li>
            <a href="/mozilla-demos/scissoring">Scissoring</a>
          </li>
          <li>
            <a href="/mozilla-demos/scissoring-animation">Scissoring animation</a>
          </li>
          <li>
            <a href="/mozilla-demos/scissor-square-game">Scissor square game</a>
          </li>
          <li>
            <a href="/mozilla-demos/basic-glsl-shader">Basic GLSL shader</a>
          </li>
        </ul>
      </div>
    </div>
  </section>
</template>

<style scoped>
.hero-body {
  align-items: self-start !important;
  justify-content: center;
}

ul {
  padding-left: 40px;
  padding-top: 20px;
}

li {
  padding-bottom: 5px;
}

a {
  color: black;
  text-decoration: underline;
}
</style>