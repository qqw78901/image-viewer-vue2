<template>
  <div class="image-viewer-vue2">
    <div class="pswp" tabindex="-1" role="dialog" aria-hidden="true" ref="el">
      <div class="pswp__bg" />

      <div class="pswp__scroll-wrap">
        <div class="pswp__container">
          <div class="pswp__item" />
          <div class="pswp__item" />
          <div class="pswp__item" />
        </div>

        <div class="pswp__ui pswp__ui--hidden">
          <div class="pswp__top-bar">
            <div class="pswp__counter" />

            <button class="pswp__button pswp__button--close" title="关闭" />

            <button class="pswp__button pswp__button--zoom" title="放大/缩小" />

            <div class="pswp__preloader">
              <div class="pswp__preloader__icn">
                <div class="pswp__preloader__cut">
                  <div class="pswp__preloader__donut" />
                </div>
              </div>
            </div>
          </div>

          <div class="pswp__share-modal pswp__share-modal--hidden pswp__single-tap">
            <div class="pswp__share-tooltip" />
          </div>

          <button class="pswp__button pswp__button--arrow--left" title="上一张" />

          <button class="pswp__button pswp__button--arrow--right" title="下一张" />

          <div class="pswp__caption">
            <div class="pswp__caption__center" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import PhotoSwipe from 'photoswipe'
import PhotoSwipeUIDefault from 'photoswipe/dist/photoswipe-ui-default'
import 'photoswipe/dist/photoswipe.css'
import 'photoswipe/dist/default-skin/default-skin.css'

export default {
  name: 'ImageViewerVue2',
  model: {
    prop: 'openIndex',
    event: 'change'
  },
  props: {
    openIndex: {
      type: Number,
      default: -1
    },
    imgs: {
      type: Array
    },
    options: {
      type: Object,
      default() {
        return {}
      }
    }
  },
  watch: {
    openIndex(newValue) {
      if (newValue > -1) {
        if (this.gallery) {
          this.gallery.goTo(newValue)
        } else {
          this.open(newValue)
        }
      } else {
        this.close()
      }
    }
  },
  data() {
    return {}
  },
  methods: {
    close() {
      if (this.gallery) {
        this.gallery.close()
        this.gallery = undefined;
      }
    },
    open(index = 0) {
      const {imgs = [], options = {}} = this
      if (!imgs.length) return
      const pswpElement = this.$refs.el
      const items = imgs.map(item => ({
        src: item,
        w: 0,
        h: 0
      }))
      // define options (if needed)
      const opts = {
        // optionName: 'option value'
        index, // start at first slide
        ...options
      }
      // Initializes and opens PhotoSwipe

      this.gallery = new PhotoSwipe(
        pswpElement,
        PhotoSwipeUIDefault,
        items,
        opts
      )
      this.gallery.listen('imageLoadComplete', (galleryIndex, item) => {
        if (!item.w || !item.h) {
          const img = new Image()
          img.src = item.src
          img.onload = () => {
            item.w = img.width
            item.h = img.height
            this.gallery.invalidateCurrItems()
            this.gallery.updateSize(true)
          }
        }
      })
      this.gallery.listen('destroy', () => {
        this.$emit('change', -1)
        this.$emit('close')
      });
      let timer=0;
      this.gallery.listen('afterChange',()=>{
        clearTimeout(timer);
        setTimeout(() => {
          let newIndex = this.gallery.getCurrentIndex();
          this.$emit('change',newIndex);
        }, 100);
      })

      this.gallery.init()
    }
  }
}
</script>
