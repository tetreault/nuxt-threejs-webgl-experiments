<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div class="hero-head">
        <h1 class="title has-text-centered">WebGL experiments using Nuxt</h1>
      </div>
      <div class="hero-body">
        <ul>
          <li>
            <a href="/demos/simple-color-animation">Simple color animation</a>
          </li>
          <li>
            <a href="/demos/color-masking">Color masking</a>
          </li>
        </ul>
      </div>
    </div>
  </section>
</template>

<style scoped>
.hero-body {
  align-items: self-start !important;
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