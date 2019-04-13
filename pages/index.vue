<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div class="hero-head">
        <h1 class="title has-text-centered">ThreeJS/WebGL experiments using Nuxt</h1>
      </div>
      <div class="hero-body">
        <ul>
          <li>
            <h4>Learn Three.js Book demos (using three-fill)</h4>
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
            <a href="/learn-threejs-book/basic-mesh-scene">Basic Mesh Scene</a>
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