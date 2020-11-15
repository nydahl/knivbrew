<template>
  <div class="container">
    <div id="hero">
      <h1 class="title">
        kniv<strong :style="activeColor">brew</strong>
      </h1>
      <div class="product-shot-container">
        <img class="product-shot" :src="productSlide" alt="">
        <img ref="nextProductSlide" class="next-product-shot" :src="nextproductSlide" alt="">
      </div>
    </div>
    <section class="padding">
      <h1>Test</h1>
    </section>
    <section class="animation">
      <div id="animationContainer">
        <canvas id="animationOne" ref="animationOne" />
      </div>
      <h1 class="animationHeading">
        Animation!
      </h1>
    </section>
  </div>
</template>

<script>
import gsap from 'gsap/dist/gsap'
import ScrollTrigger from 'gsap/dist/ScrollTrigger'
gsap.registerPlugin(ScrollTrigger)

export default {
  data () {
    return {
      currentProductSlide: 1,
      productShots: 5,
      activeColor: {
        color: 'var(--main-1)'
      }
    }
  },
  computed: {
    productSlide () {
      return `/img/${this.currentProductSlide}.jpg`
    },
    nextproductSlide () {
      return `/img/${this.currentProductSlide === this.productShots ? 1 : this.currentProductSlide + 1}.jpg`
    },
    nextProductSlide () {
      return this.currentProductSlide === this.productShots ? 1 : this.currentProductSlide + 1
    }
  },
  mounted () {
    const canvas = document.getElementById('animationOne')
    const context = canvas.getContext('2d')
    this.swapImages()
    const frameCount = 80
    const images = []
    const brewer = {
      frame: 0
    }
    for (let i = 0; i < frameCount; i++) {
      const img = new Image()
      img.src = this.currentFrame(i)
      images.push(img)
    }

    gsap.to(brewer, {
      frame: frameCount - 1,
      snap: 'frame',
      scrollTrigger: {
        scrub: 0.5,
        trigger: '#animationContainer',
        start: 'top top',
        end: 'bottom bottom',
        pin: '#animationOne'
      },
      onUpdate: render
    })
    images[0].onload = render
    function render () {
      context.clearRect(0, 0, canvas.width, canvas.height)
      context.drawImage(images[brewer.frame], 0, 0)
    }
    canvas.width = 600
    canvas.height = 1000
  },
  methods: {
    swapImages () {
      setInterval(() => {
        this.$refs.nextProductSlide.classList.add('in')
        this.activeColor = { color: `var(--main-${this.nextProductSlide})` }
        setTimeout(() => {
          this.currentProductSlide = this.nextProductSlide
          this.$refs.nextProductSlide.classList.remove('in')
        }, 500)
      }, 2000)
    },
    currentFrame (index) {
      return `/img/animation/${(index + 1).toString().padStart(4, '0')}.jpg`
    }
  }
}
</script>

<style>

:root {
  --main-1: #70424b;
  --main-2: #464659;
  --main-3: #758aa3;
  --main-4: #f2c48d;
  --main-5: #f2a082;
}

.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  flex-flow: row wrap;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.title {
  font-size: 100px;
  position: relative;
  z-index: 10;
  filter: invert(0%);
}
.title strong {
  font-weight: 600;
  transition: color 500ms ease-in-out;
  color: var(--main-1);
}

#hero {
  height: 100vh;
  width: 100vw;
  display: flex;
  flex-flow: row nowrap;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.product-shot {
  height: 100%;
}
.next-product-shot {
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  opacity: 0;
}
.next-product-shot.in {
  opacity: 1;
  transition: all 500ms ease-in-out;
}

.product-shot-container {
  position: relative;
  height: 100%;
}

.animation {
  position: relative;
  width: 100%;
}

h1.animationHeading {
  position: absolute;
  top: 100vh;
  left: 50%;
  transform: translateX(-50%);
  font-size: 3rem;
}

#animationContainer {
  width: 50%;
  height: 2000px;
  margin-left: auto;
  margin-right: auto;
}

canvas#animationOne {
  max-width: 600px;
  max-height: 1000px;
}

.padding {
  width: 100%;
  background-color: var(--main-2);
}

</style>
