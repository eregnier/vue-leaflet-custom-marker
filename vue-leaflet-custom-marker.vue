<template>
  <div class="marker-wrapper">
    <slot />
  </div>
</template>
<script>
import L from 'leaflet'
import { Icon } from 'leaflet/src/layer/marker/Icon'
import { findRealParent } from 'vue2-leaflet/src/utils/utils.js'

export default {
  props: {
    marker: {
      type: Object,
      default: undefined
    },
    alignment: {
      type: String,
      default: 'center'
    },
    offsetX: {
      type: Number,
      default: 0
    },
    offsetY: {
      type: Number,
      default: 0
    }
  },
  data () {
    return {
      leafMarker: undefined
    }
  },
  mounted () {
    this.leafMarker = L.marker([this.marker.lat, this.marker.lng], {
      icon: new CustomMarker({ html: this.$el })
    })
    this.leafMarker.addTo(findRealParent(this.$parent).mapObject)
    this.computeAlignment(this.alignment)
  },
  watch: {
    alignment (alignment) {
      this.computeAlignment(alignment)
    },
    marker (marker) {
      this.leafMarker.setLatLng(marker)
    }
  },
  methods: {
    computeAlignment (alignment) {
      const div = this.$el
      let x = 0
      let y = 0
      switch (alignment) {
        case 'top':
          y -= div.offsetHeight
          x -= div.offsetHeight / 2
          break
        case 'bottom':
          x = x - div.offsetWidth / 2
          break
        case 'left':
          x = x - div.offsetWidth
          y = y - div.offsetHeight / 2
          break
        case 'right':
          y = y - div.offsetHeight / 2
          break
        case 'center':
          x -= div.offsetWidth / 2
          y -= div.offsetHeight / 2
          break
        case 'topleft':
          x = -div.offsetWidth
          y = -div.offsetHeight
          break
        case 'topright':
          y = y - div.offsetHeight
          break
        case 'bottomleft':
          x = x - div.offsetWidth
          break
        case 'bottomright':
          break
        default:
          throw new Error('Invalid alignment type of custom marker!')
      }
      div.style.left = x + this.offsetX + 'px'
      div.style.top = y + this.offsetY + 'px'
    }
  },
  beforeDestroy () {
    if (this.leafMarker) {
      this.leafMarker.remove()
    }
  }
}

export var CustomMarker = Icon.extend({
  options: {},
  createIcon: function () {
    const div = document.createElement('div')
    div.appendChild(this.options.html)
    return div
  }
})
</script>
<style scoped>
.marker-wrapper {
  position: absolute;
}
</style>